# HBS (Motor de plantilla)
- Alternativa a EJS
- [https://handlebarsjs.com/](https://handlebarsjs.com/)

## HBS y Express
[https://www.npmjs.com/package/hbs](https://www.npmjs.com/package/hbs)
```sh
npm i hbs
```

:::tip
Existe una variante: [https://github.com/ericf/express-handlebars](https://github.com/ericf/express-handlebars) 

¿Alguien sabe si es mejor?
:::

```js
// Configuración inicial
const express = require("express");
const app = express();
const port = process.env.PORT || 3001;

// Motor de plantilla
const hbs = require('hbs');
hbs.registerPartials(__dirname + '/views/partials', function (err) {});
app.set('view engine', 'hbs');
app.set("views", __dirname + "/views");

app.use(express.static(__dirname + "/public"));

// Aquí detallar rutas

// Iniciar servidor
app.listen(port, () => {
  console.log(`Example app listening at http://localhost:${port}`);
});
```

Crear carpeta `views` y un `index.hbs`

## Extensión VSC
[https://marketplace.visualstudio.com/items?itemName=RuhanRK.hbs-snippets](https://marketplace.visualstudio.com/items?itemName=RuhanRK.hbs-snippets)

## Nodemon hbs
- [https://es.stackoverflow.com/questions/268053/escuchar-cambios-en-nodejs](https://es.stackoverflow.com/questions/268053/escuchar-cambios-en-nodejs)

Por defecto nodemon no detectará los cambios de los archivos con extensión .hbs

Crear ``nodemon.json`` en la carpeta raíz
```json
{
    "ext": "js,json,hbs"
}
```

Ahora si ejecutar:
```sh
nodemon app
```

## Rutas render

Para que Express renderice este arhivo y lo combierta en HTML utilizamos:

```js
app.get("/", (req, res) => {
  res.render('index', {titulo: '<h1>Inicio con HBS</h1>'})
});

app.get("/equipo", (req, res) => {
    res.render('equipo', {
        equipo: [
            {
                id: 1,
                nombre: 'Juanito',
                habilidad: ['Javascript', 'Node.js']
            },
            {
                id: 2,
                nombre: 'Pedrito',
                habilidad: ['Php', 'Laravel']
            }
        ]
    })
});

app.get('/servicio', (req, res) => {
    res.render('servicio', {
        servicio: {
            estado: false,
            nombre: 'Servicio de programación'
        }
    })
})

app.use((req, res, next) => {
    res.status(404).render("404", {
        titulo: "404",
        descripcion: "Página no encontrada"
    })
})
```

index.hbs

```html
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Título dinámico próximamente...</title>
</head>
<body>
    <p>
        {{titulo}}
    </p>
</body>
</html>
```

## partials
[https://handlebarsjs.com/guide/partials.html](https://handlebarsjs.com/guide/partials.html)

:::warning
Todo esto funciona si tenemos configurado:
``hbs.registerPartials(__dirname + '/views/partials', function (err) {});``
:::

Estamos repitiendo código HTML por ende podemos optimizarlo:

partials/header.hbs

```html
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>{{title}}</title>
    <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.5.2/css/bootstrap.min.css" integrity="sha384-JcKb8q3iqJ61gNV9KGb8thSsNjpSL0n8PARn9HuZOnIxN0hoP+VmmDGMN5t9UJ0Z" crossorigin="anonymous">
</head>
<body class="container">
    <h1 class="mt-5">HBS</h1>
    <hr>
```

partials/footer.hbs

```html
    <div>Footer: {{year}}</div>

</body>
</html>
```

index.hbs

```handlebars
{{> header title="Página de inicio"}}
    
    <p>
        {{titulo}}
    </p>

{{> footer year=2020}}
```

## CSS, JS, img, etc
Como ya tenemos configurada nuestra carpeta public, es ahí donde añadimos los archivos en cuestión.

Ejemplo para css bootstrap: `public/css/archivo.css`
```html
<link rel="stylesheet" href="/css/bootstrap.min.css">
```

Ejemplo para JS bootstrap: `public/js/archivo.js`
```html
<script src="/js/bootstrap.min.js"></script>
```

## If
[https://handlebarsjs.com/guide/builtin-helpers.html#if](https://handlebarsjs.com/guide/builtin-helpers.html#if)

```handlebars
{{> header title="Página de Servicio"}}
    
    <p class="lead">
        {{#if servicio.estado}}
            {{servicio.nombre}}
        {{else}}
        Servicio no disponible
        {{/if}}
    </p>

{{> footer year=2022}}
```

## Each
[https://handlebarsjs.com/guide/builtin-helpers.html#each](https://handlebarsjs.com/guide/builtin-helpers.html#each)

```handlebars
{{> header title="Página de Equipo"}}
    
    <ul>
        {{#each equipo}}
        <li>{{this.id}} - {{this.nombre}} - {{this.habilidad}}</li>
        {{else}}
        <li>Sin equipo</li>
        {{/each}}
    </ul>

{{> footer year=2021}}
```

## 404
```js
// al final de todas nuestras rutas
app.use((req, res, next) => {
    res.status(404).render("404", {
        titulo: "404",
        descripcion: "Página no encontrada"
    })
})
```

```handlebars
{{> header title="Página 404"}}
    
    <p class="lead">
        {{titulo}}
    </p>
    <p>{{descripcion}}</p>
    
{{> footer year=2020}}
```

## Extra Material Kit
- [Descargar](https://www.creative-tim.com/product/material-kit)
- [Documentación](https://demos.creative-tim.com/material-kit/docs/2.0/getting-started/introduction.html)

Llevar a la carpeta public los assets descargados, tanto de js y css:
- public\css\material-kit.css
- public\js\core\bootstrap-material-design.min.js
- public\js\core\jquery.min.js
- public\js\core\popper.min.js
- public\js\plugins\bootstrap-datetimepicker.js
- public\js\plugins\moment.min.js
- public\js\plugins\nouislider.min.js
- public\js\material-kit.js

Fonts and Icons
```html
<!-- Fonts and icons -->
<link rel="stylesheet" type="text/css" href="https://fonts.googleapis.com/css?family=Roboto:300,400,500,700|Roboto+Slab:400,700|Material+Icons" />
<link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/font-awesome/latest/css/font-awesome.min.css">
```

header.hbs
```html
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>{{title}}</title>
    {{!-- <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.5.2/css/bootstrap.min.css" integrity="sha384-JcKb8q3iqJ61gNV9KGb8thSsNjpSL0n8PARn9HuZOnIxN0hoP+VmmDGMN5t9UJ0Z" crossorigin="anonymous"> --}}
    <!-- Fonts and icons -->
    <link rel="stylesheet" type="text/css" href="https://fonts.googleapis.com/css?family=Roboto:300,400,500,700|Roboto+Slab:400,700|Material+Icons" />
    <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/font-awesome/latest/css/font-awesome.min.css">
    {{!-- Materialkit --}}
    <link rel="stylesheet" href="/css/material-kit.min.css">

</head>
<body class="container">
    {{> navbar}}
    <h1 class="mt-5">HBS</h1>
    <hr>
```

footer.hbs
```html
    <div>Footer: {{year}}</div>

<!--   Core JS Files   -->
<script src="/js/core/jquery.min.js" type="text/javascript"></script>
<script src="/js/core/popper.min.js" type="text/javascript"></script>
<script src="/js/core/bootstrap-material-design.min.js" type="text/javascript"></script>
<script src="/js/plugins/moment.min.js"></script>
<!--	Plugin for the Datepicker, full documentation here: https://github.com/Eonasdan/bootstrap-datetimepicker -->
<script src="/js/plugins/bootstrap-datetimepicker.js" type="text/javascript"></script>
<!--  Plugin for the Sliders, full documentation here: http://refreshless.com/nouislider/ -->
<script src="/js/plugins/nouislider.min.js" type="text/javascript"></script>


<!-- Control Center for Material Kit: parallax effects, scripts for the example pages etc -->
<script src="/js/material-kit.js" type="text/javascript"></script></body>
</body>
</html>
```

navbar.hbs
```html
<nav class="navbar bg-primary navbar-expand-lg">
  <div class="container">
      <div class="navbar-translate">
        <a class="navbar-brand" href="https://demos.creative-tim.com/material-kit/index.html">
          Material Kit </a>

        <button class="navbar-toggler" type="button" data-toggle="collapse" aria-expanded="false" aria-label="Toggle navigation">
            <span class="sr-only">Toggle navigation</span>
            <span class="navbar-toggler-icon"></span>
            <span class="navbar-toggler-icon"></span>
            <span class="navbar-toggler-icon"></span>
        </button>
      </div>

      <div class="collapse navbar-collapse">
          <ul class="navbar-nav ml-auto">
            <li class="nav-item">
                <a href="/" class="nav-link">
                    <i class="material-icons">apps</i> Inicio
                </a>
            </li>
            <li class="nav-item">
                <a href="/equipo" class="nav-link">
                    <i class="material-icons">apps</i> Equipo
                </a>
            </li>
            <li class="nav-item">
                <a href="/servicio" class="nav-link">
                    <i class="material-icons">apps</i> Servicio
                </a>
            </li>
          </ul>
      </div>
  </div>
</nav>
```

login.hbs (Próximamente...)

## Despliegue Heroku
Vamos a subir nuestra aplicación a un hosting real, para ello utilizaremos Heroku: [https://www.heroku.com/pricing](https://www.heroku.com/pricing)

#### Crear cuenta gratis
[https://signup.heroku.com/](https://signup.heroku.com/)

#### Cambiar puerto
[https://help.heroku.com/P1AVPANS/why-is-my-node-js-app-crashing-with-an-r10-error](https://help.heroku.com/P1AVPANS/why-is-my-node-js-app-crashing-with-an-r10-error)
```js
const PORT = process.env.PORT || 3000;
app.listen(PORT, () => {
    console.log(`Our app is running on port ${ PORT }`);
});
```

#### Agregar Script package.json
```json
"scripts": {
    "start": "node app"
  },
```

#### .gitignore
Crear archivo ``.gitignore``
```
node_modules
```

#### Heroku cli
[https://devcenter.heroku.com/articles/heroku-cli](https://devcenter.heroku.com/articles/heroku-cli)

#### Heroku GIT
<iframe width="560" height="315" src="https://www.youtube.com/embed/hWglK8nWh60" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

```
heroku login
git init
heroku git:remote -a nombre-de-su-proyecto
git add .
git commit -am "make it better"
git push heroku master
```

#### Bailar y compartir tu web en los comentarios de Youtube
<img :src="$withBase('/img/celebrar-2.gif')">

