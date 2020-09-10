# Router
Vamos a establecer un orden con el middleware Router de Express.

## Rutas
[https://expressjs.com/es/guide/routing.html](https://expressjs.com/es/guide/routing.html)

Utilice el middleware express.Router para crear manejadores de rutas montables y modulares.

Creamos una carpeta ``router``

```js
// RutasWeb.js
const express = require('express');
const router = express.Router();

router.get('/', (req, res) => {
    // console.log(__dirname)
    res.render("index", {titulo : "mi titulo dinámico"})
})

router.get('/servicios', (req, res) => {
    res.render("servicios", {tituloServicios: "Este es un mensaje dinámico de servicios"})
})

module.exports = router;
```

```js{14}
// app.js
const express = require('express');
const app = express();

const port = process.env.PORT || 3000;

// motor de plantillas
app.set('view engine', 'ejs');
app.set('views', __dirname + '/views');

app.use(express.static(__dirname + "/public"));

//Rutas web
app.use('/', require('./router/RutasWeb'));

app.use((req, res, next) => {
    res.status(404).render("404", {
        titulo: "404",
        descripcion: "Título del sitio web"
    })
})

app.listen(port, () => {
    console.log('servidor a su servicio en el puerto', port)
})
```

## Notas importantes
Puedes utilizar:
```js
app.use('/api', require('./router/RutasWeb'));
```

Pero tendremos que cambiar las rutas en las vistas (sacar el punto):
```html
<link rel="stylesheet" href="/css/bootstrap.min.css">

<script src="/js/bootstrap.min.js"></script>
```

Por ahora eso es todo con las rutas, pero seguiremos configurando en el apartado de Bases de datos.

## Mascotas Router
```js
// Mascotas.js
const express = require('express');
const router = express.Router();

router.get('/', (req, res) => {
    res.render("mascotas", {listaMascotas: "Aquí irán todas las mascotas"})
})

module.exports = router;
```

```js
//Rutas web
app.use('/', require('./router/RutasWeb'));
app.use('/mascotas', require('./router/Mascotas'));
```

```html
<%- include("template/cabecera", {tituloWeb: 'Mascotas'}) %>

    <h1><%= listaMascotas %></h1>
    
<%- include("template/footer") %>
```