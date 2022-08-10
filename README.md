# API-DOCS

## First setup packages
> express, dotenv, nodemon, morgan 
```js
require('dotenv').config()
```
```js
// http logger
if (process.env.NODE_ENV === 'development') {
    app.use(morgan('dev'))
}
```
<br/>
<br/>

## package.json commands
```js
npm init -y
```
### packge.json configuration
```js
    "start": "NODE_ENV=production node server",
    "dev": "nodemon server"
```
### git initialisation
- `git init` in the root
- add a `gitignore` file in the root and add `.env` file and `node_modules/`
<br/>
<br/>

## Restful API structure
![Capture](https://user-images.githubusercontent.com/77200870/183821759-fbdec3c3-613f-484d-9ca0-2413cf2a9e65.PNG)
<br/>
<br/>

## Routes and controllers setup
> in the server file
```js
app.use(base route, routesFile)
```
> in the routes file
```js
router.method(sub route, controller)
```
> in the controller file
```js
exports.methodName = (req, res) => {
    // login goes in here
}
```
<br/>
<br/>

## Connecting to the database
> Install mongoose
> Put the connection URI in the `.env` file
> Inside the `config` folder create a `db.js` file
```js
const mongoose = require('mongoose')

const connectDB = async (url) => {
    return await mongoose.connect(url)
}

module.exports = connectDB
```
