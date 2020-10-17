# Login + JWT
Vamos a crear un login agregando seguridad con JWT.

## JWT
JWT (JSON Web Token): [https://jwt.io/](https://jwt.io/)
1. Es un token de seguridad que nosotros creamos al momento que el usuario se registra con sus credenciales.
2. Este token se devuelve al cliente el cual tendrá que enviar cada vez que solicita información al servidor.
3. Se divide en 3 partes: Header, Payload y Verify Signature: Revisar: [https://jwt.io/](https://jwt.io/)

Recursos:
* [https://openwebinars.net/blog/que-es-json-web-token-y-como-funciona/](https://openwebinars.net/blog/que-es-json-web-token-y-como-funciona/)
* [https://www.developerro.com/2019/03/12/jwt-api-authentication/](https://www.developerro.com/2019/03/12/jwt-api-authentication/)

Ya que sabemos más o menos como se comportará nuestro login, es momento de configurar nuestro servidor con Express y JWT.

## API REST
Para este ejercicio práctico realizaremos una API REST, que un estándar (reglas y especificaciones) para la transferencia de información entre cliente y servidor, utilizaremos respuestas en JSON y los típicos verbos HTTP: POST, GET, DELETE, PUT.

Ventajas de hacer una API REST (existen más, se los dejo como tarea):
* Podemos conectar múltiples aplicaciones a nuestro servidor, ya sea página web, aplicación móvil, aplicación para escritorio, etc.
* Youtube por ejemplo cuenta con una api rest para poder implementar sus videos ya sea desde el sitio web o su aplicación móvil.

## Guía
Nos basaremos en el siguiente tutorial: [https://codeburst.io/to-handle-authentication-with-node-js-express-mongo-jwt-7e55f5818181](https://codeburst.io/to-handle-authentication-with-node-js-express-mongo-jwt-7e55f5818181)

## Configuración inicial
Requisitos:
1. Haber realizado el curso de node.js Fundamentos: [https://www.youtube.com/watch?v=mG4U9t5nWG8&list=PLPl81lqbj-4IEnmCXEJeEXPepr8gWtsl6](https://www.youtube.com/watch?v=mG4U9t5nWG8&list=PLPl81lqbj-4IEnmCXEJeEXPepr8gWtsl6)
2. Tener instalado node.js [https://nodejs.org/es/](https://nodejs.org/es/)
3. Instalar Postman [https://www.postman.com/downloads/](https://www.postman.com/downloads/)

```
npm init -y
```

```
Crear index.js
```

```
npm i express
npm i body-parser
npm i mongoose
npm i bcrypt
npm i dotenv
npm i jsonwebtoken
npm i @hapi/joi
npm i -g nodemon
npm i cors
```

package.json
```json
"scripts": {
    "dev": "nodemon index",
    "start": "npm run index"
},
```

.gitignore
```
node_modules
.env
```

carpetas
```
routes
models
```

index.js
```js
const express = require('express');
const mongoose = require('mongoose');
const bodyparser = require('body-parser');
require('dotenv').config()

const app = express();

// capturar body
app.use(bodyparser.urlencoded({ extended: false }));
app.use(bodyparser.json());

// Conexión a Base de datos

// import routes

// route middlewares
app.get('/', (req, res) => {
    res.json({
        estado: true,
        mensaje: 'funciona!'
    })
});

// iniciar server
const PORT = process.env.PORT || 3001;
app.listen(PORT, () => {
    console.log(`servidor andando en: ${PORT}`)
})
```

.env
```
USER=
PASSWORD=
DBNAME=
TOKEN_SECRET=
```

Ejecutar servidor
```
npm run dev
```

Hacer petición en Postman
```
http://localhost:3001/
```

## Rutas
routes/auth.js
```js
const router = require('express').Router();

router.post('/register', async (req, res) => {

    res.json({
        error: null,
        data: 'aquí va ir la data'
    })
})

module.exports = router;
```

index.js
```js
// import routes
const authRoutes = require('./routes/auth');

// route middlewares
app.use('/api/user', authRoutes);
```

probar en postman:
```
http://localhost:3001/api/user/register
```

## MongoDB
[https://cloud.mongodb.com/](https://cloud.mongodb.com/)

Crear usuario Database Access y agregar a .env
```
USER=juanito
PASSWORD=juanito
DBNAME=apiJuanito
```

index.js
```js
// Conexión a Base de datos
const uri = `mongodb+srv://${process.env.USER}:${process.env.PASSWORD}@cluster0.ncdk5.mongodb.net/${process.env.DBNAME}?retryWrites=true&w=majority`;
mongoose.connect(uri,
    { useNewUrlParser: true, useUnifiedTopology: true }
)
.then(() => console.log('Base de datos conectada'))
.catch(e => console.log('error db:', e))
```

## Schema
models/User.js
```js
const mongoose = require('mongoose');

const userSchema = mongoose.Schema({
    name: {
        type: String,
        required: true,
        min: 6,
        max: 255
    },
    email: {
        type: String,
        required: true,
        min: 6,
        max: 1024
    },
    password: {
        type: String,
        required: true,
        minlength: 6
    },
    date: {
        type: Date,
        default: Date.now
    }
})

module.exports = mongoose.model('User', userSchema);
```

## Registro
routes/auth.js

```js
const router = require('express').Router();
const User = require('../models/User');

router.post('/register', async (req, res) => {

    const user = new User({
        name: req.body.name,
        email: req.body.email,
        password: password
    });
    try {
        const savedUser = await user.save();
        res.json({
            error: null,
            data: savedUser
        })
    } catch (error) {
        res.status(400).json({error})
    }
})

module.exports = router;
```

Probar en Postman: `http://localhost:3001/api/user/register`

Body/raw/json
```json
{
    "name":"prueba3",
    "email":"prueba3@prueba.com",
    "password": "123123"
}
```

Verificar [https://cloud.mongodb.com/](https://cloud.mongodb.com/)

## Validaciones @hapi/joi
```
npm i @hapi/joi
```

routes/auth.js
```js
// validation
const Joi = require('@hapi/joi');

const schemaRegister = Joi.object({
    name: Joi.string().min(6).max(255).required(),
    email: Joi.string().min(6).max(255).required().email(),
    password: Joi.string().min(6).max(1024).required()
})

router.post('/register', async (req, res) => {

    // validate user
    const { error } = schemaRegister.validate(req.body)
    
    if (error) {
        return res.status(400).json(
            {error: error.details[0].message}
        )
    }

    const user = new User({
        name: req.body.name,
        email: req.body.email,
        password: password
    });
    try {
        const savedUser = await user.save();
        res.json({
            error: null,
            data: savedUser
        })
    } catch (error) {
        res.status(400).json({error})
    }
})

module.exports = router;
```

## Validar email único
```js
const isEmailExist = await User.findOne({ email: req.body.email });
if (isEmailExist) {
    return res.status(400).json(
        {error: 'Email ya registrado'}
    )
}
```

## Hash contraseña
```
npm i bcrypt
```
```js
// constraseña
const bcrypt = require('bcrypt');

router.post('/register', async (req, res) => {

    // validate user
    const { error } = schemaRegister.validate(req.body)
    
    if (error) {
        return res.status(400).json({error: error.details[0].message})
    }

    const isEmailExist = await User.findOne({ email: req.body.email });
    if (isEmailExist) {
        return res.status(400).json({error: 'Email ya registrado'})
    }

    // hash contraseña
    const salt = await bcrypt.genSalt(10);
    const password = await bcrypt.hash(req.body.password, salt);

    const user = new User({
        name: req.body.name,
        email: req.body.email,
        password: password
    });
    try {
        const savedUser = await user.save();
        res.json({
            error: null,
            data: savedUser
        })
    } catch (error) {
        res.status(400).json({error})
    }
})

module.exports = router;
```

## Código completo
```js
const router = require('express').Router();
const User = require('../models/User');
const jwt = require('jsonwebtoken');

// constraseña
const bcrypt = require('bcrypt');

// validation
const Joi = require('@hapi/joi');

const schemaRegister = Joi.object({
    name: Joi.string().min(6).max(255).required(),
    email: Joi.string().min(6).max(255).required().email(),
    password: Joi.string().min(6).max(1024).required()
})

const schemaLogin = Joi.object({
    email: Joi.string().min(6).max(255).required().email(),
    password: Joi.string().min(6).max(1024).required()
})

router.post('/register', async (req, res) => {

    // validate user
    const { error } = schemaRegister.validate(req.body)
    
    if (error) {
        return res.status(400).json({error: error.details[0].message})
    }

    const isEmailExist = await User.findOne({ email: req.body.email });
    if (isEmailExist) {
        return res.status(400).json({error: 'Email ya registrado'})
    }

    // hash contraseña
    const salt = await bcrypt.genSalt(10);
    const password = await bcrypt.hash(req.body.password, salt);

    const user = new User({
        name: req.body.name,
        email: req.body.email,
        password: password
    });
    try {
        const savedUser = await user.save();
        res.json({
            error: null,
            data: savedUser
        })
    } catch (error) {
        res.status(400).json({error})
    }
})

module.exports = router;
```

## Login
```js
const schemaLogin = Joi.object({
    email: Joi.string().min(6).max(255).required().email(),
    password: Joi.string().min(6).max(1024).required()
})

router.post('/login', async (req, res) => {
    // validaciones
    const { error } = schemaLogin.validate(req.body);
    if (error) return res.status(400).json({ error: error.details[0].message })
    
    const user = await User.findOne({ email: req.body.email });
    if (!user) return res.status(400).json({ error: 'Usuario no encontrado' });

    const validPassword = await bcrypt.compare(req.body.password, user.password);
    if (!validPassword) return res.status(400).json({ error: 'contraseña no válida' })
    
    res.json({
        error: null,
        data: 'exito bienvenido'
    })
})
```

Probar en Postman: `http://localhost:3001/api/user/login`
Body/raw/json
```json
{
    "email": "",
    "password": ""
}
```

## JWT
```
npm i jsonwebtoken
```

```js
const jwt = require('jsonwebtoken');

router.post('/login', async (req, res) => {
    // validaciones
    ...

    // create token
    const token = jwt.sign({
        name: user.name,
        id: user._id
    }, process.env.TOKEN_SECRET)
    
    res.header('auth-token', token).json({
        error: null,
        data: {token}
    })
})
```

.env
```
TOKEN_SECRET=secreto
```

1. Probar en Postman para visualizar token
2. Agregar token a [https://jwt.io/](https://jwt.io/)

## Middleware (rutas protegidas)
routes/validate-token.js
```js
const jwt = require('jsonwebtoken')

// middleware to validate token (rutas protegidas)
const verifyToken = (req, res, next) => {
    const token = req.header('auth-token')
    if (!token) return res.status(401).json({ error: 'Acceso denegado' })
    try {
        const verified = jwt.verify(token, process.env.TOKEN_SECRET)
        req.user = verified
        next() // continuamos
    } catch (error) {
        res.status(400).json({error: 'token no es válido'})
    }
}

module.exports = verifyToken;
```

## Ruta protegida
routes/dashboard.js
```js
const router = require('express').Router();

router.get('/', (req, res) => {
    res.json({
        error: null,
        data: {
            title: 'mi ruta protegida',
            user: req.user
        }
    })
})

module.exports = router
```

index.js
```js
// import routes
const dashboadRoutes = require('./routes/dashboard');
const verifyToken = require('./routes/validate-token');

// route middlewares
app.use('/api/dashboard', verifyToken, dashboadRoutes);
```

1. Postman: `http://localhost:3001/api/dashboard`
2. Headers: auth-token: agregar token
3. Ver respuesta server

## Consumir api
Queda pendiente consumir la api desde el cliente... vue.js??