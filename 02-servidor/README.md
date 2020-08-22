# Servidor HTTP

Como vimos la sección anterior es el turno de construir nuestro servidor:

## Módulo HTTP

Node.js viene con algunos modulos que no necesitan instalación, uno de ellos es el http.

[https://nodejs.dev/learn/build-an-http-server](https://nodejs.dev/learn/build-an-http-server)

```js
const http = require("http");

const server = http.createServer((req, res) => {
  console.log("respuesta del servidor...");
  res.end("Te envío un saludo desde el servidor con node.js");
});

const puerto = 3000;

server.listen(puerto, () => {
  console.log("Escuchando...");
});
```

:::tip

- Ahora visita: `http://localhost:3000/`
- Ctrl + c para finalizar el servidor

:::

## Nodemon

[https://www.npmjs.com/package/nodemon](https://www.npmjs.com/package/nodemon)

Esta herramienta se instala a través de npm y nos sirve para estar escuchando cambios en nuestra configuración de node.js y reinicia automáticamente el servidor.

Instalación global:

```
npm install -g nodemon
```

Ejecutar:

```
nodemon app.js
```

## Express

[https://expressjs.com/es/](https://expressjs.com/es/)

- Express es una infraestructura de aplicaciones web Node.js mínima y flexible que proporciona un conjunto sólido de características para las aplicaciones web y móviles (Framework).
- Con miles de métodos de programa de utilidad HTTP y middleware a su disposición, la creación de una API sólida es rápida y sencilla.

```
npm i express
```

## Express Hello World

[https://expressjs.com/es/starter/hello-world.html](https://expressjs.com/es/starter/hello-world.html)

```js
const express = require("express");
const app = express();
const port = 3000;

app.get("/", (req, res) => {
  res.send("Hello World!");
});

app.listen(port, () => {
  console.log(`Example app listening at http://localhost:${port}`);
});
```

Ejecute:

```
nodemon app
```

## Rutas

[https://expressjs.com/es/starter/basic-routing.html](https://expressjs.com/es/starter/basic-routing.html)

```js
app.get("/contacto", (req, res) => {
  res.send("ruta de contacto");
});
```

## Archivos Estáticos

[https://expressjs.com/es/starter/static-files.html](https://expressjs.com/es/starter/static-files.html)

Para el servicio de archivos estáticos como, por ejemplo, imágenes, archivos CSS y archivos JavaScript, utilice la función de <b>middleware</b> incorporado express.static de Express.

- Cree una carpeta `public` con un archivo `index.html`

```js
app.use(express.static(__dirname + "/public"));
```

:::tip path.join
He visto varios ejemplos con path.join, este nos sirve hacer uniones de rutas (aquí public no lleva el "/"), ¿Es necesario?

```js
app.use(express.static(path.join(__dirname, "public")));
```

:::

:::warning Importante
El orden es clave al ordenar nuestras rutas:

```js
app.get("/", (req, res) => {
  res.send("Hello World!");
});

app.use(express.static(__dirname + "/public"));
```

:::

`__dirmane` es la ruta según la máquina donde se ejecuta el código:

```js
app.get("/contacto", (req, res) => {
  res.send(__dirname);
});
```

## Middleware

- [https://expressjs.com/es/starter/faq.html](https://expressjs.com/es/starter/faq.html)
- [https://expressjs.com/es/guide/using-middleware.html](https://expressjs.com/es/guide/using-middleware.html)

En palabras simples es una acción que se ejecuta antes de nuestra función de ruta.

<!-- :::tip
app.use(): "use" responde a todos los verbos http.
::: -->

- Configurar "Página 404"

<img
      :src="$withBase('/img/404.gif')"
      alt=""
    />

```html
<!DOCTYPE html>
<html lang="es">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Document</title>
  </head>

  <body>
    <h2>Esta es la página 404</h2>
    <img
      src="https://sparktco.com/wp-content/uploads/2019/07/perdido.gif"
      alt=""
    />
  </body>
</html>
```

```js
app.use((req, res, next) => {
  // res.status(404).send("Sorry cant find that!");
  res.status(404).sendFile(__dirname + "/public/404.html");
});
```

En este caso `sendFile` abre un archivo en específico.

## ¿Qué sigue?

Hasta el momento hemos trabajado con archivos estáticos pero la gracia de Express es que podemos utilizar gestores o motores de plantillas HTML, lo que nos facilitará la vida al momento de trabajar con bases de datos. Continuemos en la siguiente sección :)
