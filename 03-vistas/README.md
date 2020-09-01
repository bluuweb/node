# Vistas

Si bien podríamos trabajar con archivos estáticos HTML, existe una manera más dinámica de crear nuestros sitios web utilizando motores de plantillas.

Entre los más populares (ejs|hbs|hjs|jade|pug|twig|vash)

[https://expressjs.com/en/resources/template-engines.html](https://expressjs.com/en/resources/template-engines.html)

## Motores de plantilla

[https://expressjs.com/es/guide/using-template-engines.html](https://expressjs.com/es/guide/using-template-engines.html)

Vamos a utilizar EJS:

- No perdemos nuestro html clásico y agregamos lógica con EJS.
- [https://ejs.co/](https://ejs.co/)

## EJS y Express

```
npm i ejs
```

```js
// Motor de plantilla
app.set("view engine", "ejs");
app.set("views", __dirname + "/views");
```

Crear carpeta `views` y un `index.ejs`

## Rutas render

Para que Express renderice este arhivo y lo combierta en HTML utilizamos:

```js
app.get("/", (req, res) => {
  res.render("index", { titulo: "inicio EJS" });
});

app.get("/nosotros", (req, res) => {
  res.render("nosotros", { titulo: "Nosotros EJS" });
});

app.use((req, res, next) => {
  res.status(404).render("404", { titulo: "Página 404" });
});
```

index.ejs

```html
<!DOCTYPE html>
<html lang="es">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Document</title>
  </head>

  <body>
    <h1>Hola a EJS <%= titulo %></h1>
  </body>
</html>
```

## Include

Estamos repitiendo código HTML por ende podemos optimizarlo con include:

template/cabecera.ejs

```html
<!DOCTYPE html>
<html lang="es">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title><%= tituloweb %></title>
</head>

<body>
```

index.ejs

```html
<%- include('template/cabecera', {tituloweb: 'Inicio EJS'}); %>
<h1>Hola a EJS <%= titulo %></h1>
</body>

</html>
```

## CSS, JS, img, etc
Como ya tenemos configurada nuestra carpeta public, es ahí donde añadimos los archivos en cuestión.

cabecera.ejs
```html
<!DOCTYPE html>
<html lang="es">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title><%= tituloweb %></title>
    <link rel="stylesheet" href="./bootstrap.min.css">
</head>

<body class="container">

    <%- include('navbar') %>
```

navbar.ejs
```html
<nav class="navbar navbar-expand-lg navbar-dark bg-dark">
    <a class="navbar-brand" href="/">EJS Express</a>
    <button class="navbar-toggler" type="button" data-toggle="collapse" data-target="#navbarNav" aria-controls="navbarNav" aria-expanded="false" aria-label="Toggle navigation">
        <span class="navbar-toggler-icon"></span>
    </button>
    <div class="collapse navbar-collapse" id="navbarNav">
        <ul class="navbar-nav ml-auto">
        <li class="nav-item">
            <a class="nav-link" href="/">Home <span class="sr-only">(current)</span></a>
        </li>
        <li class="nav-item">
            <a class="nav-link" href="/nosotros">Nosotros</a>
        </li>
        <li class="nav-item">
            <a class="nav-link" href="/contacto">Contacto</a>
        </li>
        </ul>
    </div>
</nav>
```

footer.ejs
```html
    <script src="https://code.jquery.com/jquery-3.5.1.slim.min.js" integrity="sha384-DfXdz2htPH0lsSSs5nCTpuj/zy4C+OGpamoFVy38MVBnE+IbbVYUew+OrCXaRkfj" crossorigin="anonymous"></script>
    <script src="./js/bootstrap.min.js"></script>
</body>
</html>
```

index.ejs
```html
<%- include('template/cabecera', {tituloweb: 'Inicio EJS'}); %>

<h1>Hola a EJS <%= titulo %></h1>

<%- include('template/footer'); %>
```

## Despliegue Heroku
Vamos a subir nuestra aplicación a un hosting real, para ello utilizaremos Heroku: [https://www.heroku.com/pricing](https://www.heroku.com/pricing)

#### Crear cuenta gratis
[https://signup.heroku.com/](https://signup.heroku.com/)

#### Cambiar puerto
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

