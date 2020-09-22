# MongoDB
Existen varias formas de trabajar con bases de datos, por ahora comenzaremos con MongoDB, base de datos no relacional y que nos permite trabajar con la nube sin necesidad de hacer mayores instalaciones en nuestro equipo.

[https://www.mongodb.com/](https://www.mongodb.com/)

## Cluster
Iniciar sesión: [https://cloud.mongodb.com/](https://cloud.mongodb.com/)

Crear nuestro Cluster: puedes dejar todo por defecto para que no exitan cargos monetarios en tu cuenta :)

:::tip Cluster
El plan gratuito está limitado a un Cluster
:::

## Access
Vamos a crear un nuevo usuario en Security -> Database Access.

## Connet
Vamos a utilizar ``connect your application`` 
```html
mongodb+srv://<username>:<password>@cluster0.ncdk5.mongodb.net/<dbname>?retryWrites=true&w=majority
```

## Mongoose
Es una forma sencilla de establecer conexión con MongoDB.

[https://mongoosejs.com/docs/index.html](https://mongoosejs.com/docs/index.html)

```
npm i mongoose
```

## App.js
```js
const mongoose = require('mongoose');

const usuario = "usuariodb"
const password = "passdb"
const dbName = "veterinaria"

const uri = `mongodb+srv://${usuario}:${password}@cluster0.ncdk5.mongodb.net/${dbName}?retryWrites=true&w=majority`;

mongoose.connect(uri, { useNewUrlParser: true, useUnifiedTopology: true })
  .then(()=> console.log('conectado a mongodb')) 
  .catch(e => console.log('error de conexión', e))
```

## Schema
Todo comienza con un esquema, esto asigna a colección de mongoDB definiendo la forma de los futuros documentos de dicha colección: `models->mascota.js`
[https://mongoosejs.com/docs/guide.html#definition](https://mongoosejs.com/docs/guide.html#definition)

```js
const mongoose = require('mongoose');
const Schema = mongoose.Schema;

const mascotaSchema = new Schema({
  nombre:  String,
  descripcion: String
});

// Crear el modelo
const Mascota = mongoose.model('Mascota', mascotaSchema);

module.exports = Mascota;
```

## find()
[https://mongoosejs.com/docs/api.html#model_Model.find](https://mongoosejs.com/docs/api.html#model_Model.find)

```js
const express = require('express');
const router = express.Router();

const Mascota = require('../models/mascota')

router.get('/', async (req, res) => {
    try {
        const arrayMascotas = await Mascota.find();
        console.log(arrayMascotas)
        res.render("mascotas", {
            listaMascotas: "Aquí irán todas las mascotas",
            arrayMascotas
        })
    } catch (error) {
        console.log(error)
    }
})

module.exports = router;
```

```ejs
<%- include("template/cabecera", {tituloWeb: 'Mascotas'}) %>

    <h1><%= listaMascotas %></h1>

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
         
        <% if (arrayMascotas.length > 0) { %>
            <% arrayMascotas.forEach(mascota => { %>
                <tr>
                    <th scope="row"><%= mascota.id %></th>
                    <td><%= mascota.nombre %> </td>
                    <td><%= mascota.descripcion %></td>
                    <td>@mdo</td>
                  </tr>
            <% }) %>
        <% } %>
        </tbody>
      </table>
    
<%- include("template/footer") %>
```

## Varibles de entorno

1. [https://www.npmjs.com/package/dotenv](https://www.npmjs.com/package/dotenv)

```
npm install dotenv
```

2. Configurar
```js
require('dotenv').config()
```

3. Crear archivo ``.env``
```
PORT=3001
USUARIO=xxx
PASSWORD=xxx
DBNAME=xxx
```

4. Llamar
```js
const uri = `mongodb+srv://${process.env.USUARIO}:${process.env.PASSWORD}@cluster0.ncdk5.mongodb.net/${process.env.DBNAME}?retryWrites=true&w=majority`;
```

5. gitignore
```
node_modules
.env
```

6. Heroku Settings: Agregar variables "Config Vars" [https://devcenter.heroku.com/articles/config-vars](https://devcenter.heroku.com/articles/config-vars)
7. Deploy :)