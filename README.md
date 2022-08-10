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
> In `server.js` add the db connection function
```js
const PORT = process.env.PORT || 5000

const start = async () => {
    try {
        const conn = await connectDB(process.env.MONGO_URI)
        console.log(`MongoDB connected successfully: ${conn.connection.host}`)
        app.listen(PORT, () => {
            console.log(`Server listening in ${process.env.NODE_ENV} mode on port ${PORT}`)
        })
    } catch (error) {
        console.log(error)
        process.exit(1)
    }
}

start()
```
<br/>
<br/>

## Models
> Add the models in a `models` folder (as a convention they must be capatilized)
```js
const mongoose = require('mongoose')

const ModelSchema = new mongoose.Schema({
    // model body
}, {timestamps: true})

const Model = mongoose.model('Model', ModelSchema)

module.exports = Model
```
### Model proprties
| Option | Values |
| ------ | ----------- |
| **type**   | String, Number, [String], [Number] |
| **required**   | true, false |
| **default**   | true, 'no-inage.jpg' |
| **trim**   | true, false |
| **maxlength**   | 50, [100, 'error message'] |
| **minlength**   | 50, [100, 'error message'] |
| **match**   | regex, [regex, 'error message'] |

<details>
<summary>Show full example</summary>
<p>
```js
 const BootcampSchema = new mongoose.Schema({
  name: {
    type: String,
    required: [true, 'Please add a name'],
    unique: true,
    trim: true,
    maxlength: [50, 'Name can not be more than 50 characters']
  },
  slug: String,
  description: {
    type: String,
    required: [true, 'Please add a description'],
    maxlength: [500, 'Description can not be more than 500 characters']
  },
  website: {
    type: String,
    match: [
        /(https?:\/\/(?:www\.|(?!www))[a-zA-Z0-9][a-zA-Z0-9-]+[a-zA-Z0-9]\.[^\s]{2,}|www\.[a-zA-Z0-9][a-zA-Z0-9-]+[a-zA-Z0-9]\.[^\s]{2,}|https?:\/\/(?:www\.|(?!www))[a-zA-Z0-9]+\.[^\s]{2,}|www\.[a-zA-Z0-9]+\.[^\s]{2,})/,
        'Please use a valid URL with HTTP or HTTPS'
    ]
  },
  phone: {
    type: String,
    maxlength: [20, 'Please enter a valid phone number']
  },
  email: {
    type: String,
    match: [
        /^[a-zA-Z0-9.!#$%&'*+/=?^_`{|}~-]+@[a-zA-Z0-9](?:[a-zA-Z0-9-]{0,61}[a-zA-Z0-9])?(?:\.[a-zA-Z0-9](?:[a-zA-Z0-9-]{0,61}[a-zA-Z0-9])?)*$/,
        'Please enter a valid email'
    ]
  },
  address: {
    type: String,
    required: [true, 'Please enter an address']
  },
  location: {
    type: {
        type: String,
        enum: ['Point'],
        required: true,
    },
    coordinates: {
        type: [Number],
        required: true,
        index: '2dshpere'
    },
    formattedAddress: String,
    street: String,
    city: String,
    state: String,
    zipcode: String,
    country: String,
  },
  carrers: {
    type: [String],
    enum: [
        'Web Development',
        'Mobile Development',
        'UI/UX',
        'Data Science',
        'Business',
        'Other'
    ]
  },
  averageRating: {
    type: Number,
    min: [1, 'Rating must be at least 1'],
    max: [10, 'Rating can not be more than 10'],
  },
  averageCost: Number,
  photo: {
    type: String,
    default: 'no-photo.jpg'
  },
  housing: {
    type: Boolean,
    default: false
  },
  jobAssistance: {
    type: Boolean,
    default: false
  },
  jobGuarantee: {
    type: Boolean,
    default: false
  },
  acceptGi: {
    type: Boolean,
    default: false,
  }
}, {timestamps: true})
```
</p>
</details>
