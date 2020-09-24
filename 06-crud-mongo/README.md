# CRUD MongoDB
Realizaremos las operaciones de leer, crear, editar y eliminar documentos utilizando MongoDB y Express.

## Leer
En la sección anterior conocimos como conectarnos a mongoDB y leer documentos, por ende utilizaremos el mismo proyecto, si gustas lo puedes revisar aquí:

<iframe width="560" height="315" src="https://www.youtube.com/embed/IpQSsb-1N1g" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

## bodyParser
Tenemos que instalar para utilizar x-www-form-urlencoded

[https://www.npmjs.com/package/body-parser](https://www.npmjs.com/package/body-parser)

```
npm i body-parser
```

[https://www.npmjs.com/package/body-parser#expressconnect-top-level-generic](https://www.npmjs.com/package/body-parser#expressconnect-top-level-generic)

```js
const express = require('express');
const bodyParser = require('body-parser')
const app = express();


// parse application/x-www-form-urlencoded
app.use(bodyParser.urlencoded({ extended: false }))
// parse application/json
app.use(bodyParser.json())
```

## Crear

Ruta
```js
router.get('/crear', (req, res) => {
    res.render('crear')
})
```

Formulario
```ejs
<%- include("template/cabecera", {tituloWeb: 'Mascotas'}) %>

  <div class="container">

    <h1>Crear nueva mascota</h1>
    
    <form action="/mascotas" class="my-2" method="POST">
    
        <input 
            type="text"
            placeholder="Ingrese Nombre"
            class="form-control my-2"
            name="nombre"
        >
    
        <input 
            type="text"
            placeholder="Ingrese Descripción"
            class="form-control my-2"
            name="descripcion"
        >
    
        <button
            type="submit"
            class="btn btn-dark btn-block"
        >
            Agregar
        </button>
    
    </form>

  </div>

    
<%- include("template/footer") %>
```

POST
```js
router.post('/', async (req, res) => {
    const body = req.body
    console.log(body)
})
```

GUARDAR Método #1:
[https://mongoosejs.com/docs/models.html](https://mongoosejs.com/docs/models.html)
```js
router.post('/', async (req, res) => {
    const body = req.body
    console.log(body)
    try {
        const mascotaDB = new Mascota(body)
        await mascotaDB.save()
        res.redirect('/mascotas')
    } catch (error) {
        console.log('error', error)
    }
})
```

GUARDAR Método #2:
[https://mongoosejs.com/docs/api.html#model_Model.create](https://mongoosejs.com/docs/api.html#model_Model.create)
```js
router.post('/', async (req, res) => {
    const body = req.body
    console.log(body)
    try {
        await Mascota.create(body)
        res.redirect('/mascotas')
    } catch (error) {
        console.log('error', error)
    }
})
```

## Get único documento

Creamos el botón para viajar a la ruta detalle:
```ejs
<tr>
  <th scope="row"><%= mascota.id %></th>
  <td><%= mascota.nombre %> </td>
  <td><%= mascota.descripcion %></td>
  <td>
    <a
      class="btn btn-warning btn-sm"
      href="mascotas/<%= mascota.id %>"
    >
      Editar
    </a>
  </td>
</tr>
```

Creamos la ruta con su respectivo render:
```js
router.get('/:id', async(req, res) => {
    const id = req.params.id
    try {
        const mascotaDB = await Mascota.findOne({ _id: id })
        console.log(mascotaDB)
        res.render('detalle', {
            mascota: mascotaDB,
            error: false
        })
    } catch (error) {
        console.log('erroooooooooorrr', error)
        res.render('detalle', {
            error: true,
            mensaje: 'No se encuentra el documento...'
        })
    }
})
```

Pintamos el detalle del documento:
```ejs
<%- include("template/cabecera", {tituloWeb: 'Mascotas'}) %>

  <div class="container">
    
    <h1>Detalle Mascota</h1>
  
    <% if (error) { %>
        <%= mensaje %> 
        <a 
            href="/mascotas"
            class="btn btn-dark btn-block my-2"
        >
            Volver
        </a>
    <% } %>

    <% if (!error) { %>
        <table class="table">
            <thead>
                <tr>
                <th scope="col">#</th>
                <th scope="col">Nombre</th>
                <th scope="col">Descripción</th>
                <th scope="col">Acción</th>
                </tr>
            </thead>
            <tbody>
                <tr>
                    <th scope="row"><%= mascota.id %></th>
                    <td><%= mascota.nombre %> </td>
                    <td><%= mascota.descripcion %></td>
                    <td>
                        <button
                        class="btn btn-danger btn-sm"
                        data-id="<%= mascota.id %>"
                        >
                        Eliminar
                        </button>
                    </td>
                </tr>
            </tbody>
        </table>
    <% } %>
     
  </div>
    
<%- include("template/footer") %>
```

## Delete documento

```ejs
<button
  class="btn btn-danger btn-sm"
  data-id="<%= mascota.id %>"
>
  Eliminar
</button>
```

Creamos la lógica del botón eliminar:
```html
<script>
// onclick vs addEventListener
// https://stackoverflow.com/questions/6348494/addeventlistener-vs-onclick#:~:text=Essentially%20onclick%20is%20a%20HTML,(using%20this%20for%20example).

const btnEliminar = document.querySelector('.btn-danger')
  btnEliminar.addEventListener('click', async() => {
    console.log(btnEliminar.dataset.id)  
    try {
        // https://developer.mozilla.org/es/docs/Web/API/HTMLElement/dataset
        const data = await fetch(`/mascotas/${btnEliminar.dataset.id}`, {
          method: 'delete'
        })
        const res = await data.json()
        console.log(res)
        if(res.estado){
            window.location.href = '/mascotas'
        }else{
            console.log(res)
        }
    } catch (error) {
        console.log(error)
    }
  })

</script>
```

Creamos la ruta correspondiente:
```js
router.delete('/:id', async (req, res) => {
    const id = req.params.id;
    console.log('id desde backend', id)
    try {

        const mascotaDB = await Mascota.findByIdAndDelete({ _id: id });
        console.log(mascotaDB)

        // https://stackoverflow.com/questions/27202075/expressjs-res-redirect-not-working-as-expected
        // res.redirect('/mascotas')
        if (!mascotaDB) {
            res.json({
                estado: false,
                mensaje: 'No se puede eliminar'
            })
        } else {
            res.json({
                estado: true,
                mensaje: 'eliminado!'
            })
        }
        
    } catch (error) {
        console.log(error)
    }
})
```

## Editar documento

Detalle.ejs
```ejs
<% if (!error) { %>
    <form id="editar" data-id="<%= mascota.id %>">
        <input 
            type="text"
            value="<%= mascota.nombre %>"
            class="form-control my-2"
            name="nombre"
            id="nombreInput"
        >
        <input 
            type="text"
            value="<%= mascota.descripcion %>"
            class="form-control my-2"
            name="descripcion"
            id="descripcionInput"
        >
        <button
            class="btn btn-warning btn-sm"
            type="submit"
        >
            Editar
        </button>
    </form>
    <hr>
    <button
        class="btn btn-danger btn-sm"
        data-id="<%= mascota.id %>"
    >
        Eliminar
    </button>
<% } %>
```

```js
const formEditar = document.querySelector('#editar');

formEditar.addEventListener('submit', async(e) => {
    e.preventDefault()
    // Alternativa #1 (capturar input)
    const nombre = formEditar.elements['nombre'].value
    // Alternativa #2 (capturar input)
    const descripcion = document.querySelector('#descripcionInput').value
    const id = formEditar.dataset.id 

    const data = await fetch(`/mascotas/${id}`, {
        method: 'PUT',
        headers: {
        'Content-Type': 'application/json'
        // 'Content-Type': 'application/x-www-form-urlencoded',
        },
        body: JSON.stringify({nombre, descripcion})
    })

    const res = await data.json()

    if(res.estado){
        window.location.href = '/mascotas'
    }else{
        console.log(res)
    }

})
```

```js
router.put('/:id', async (req, res) => {
    const id = req.params.id;
    const body = req.body;

    console.log(id)
    console.log('body', body)

    try {
        const mascotaDB = await Mascota.findByIdAndUpdate(
            id, body, { useFindAndModify: false }
        )
        console.log(mascotaDB)
        res.json({
            estado: true,
            mensaje: 'Mascota editada'
        })
    } catch (error) {
        console.log(error)
        res.json({
            estado: false,
            mensaje: 'Mascota falla'
        })
    }
})
```