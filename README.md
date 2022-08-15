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
> In the server file
```js
app.use(base route, routesFile)
```
> In the routes file
```js
router.method(sub route, controller)
```
> Install `express-async-handler`<br/>
> In the controller file<br/>
> Wrap all the methods
```js
const asyncHandler = require('express-async-handler')

exports.methodName = asyncHandler(async(req, res) => {
    // login goes in here
})
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

## Mongoose Models
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
| **unique**   | true, false |
| **select**   | true, false |
| **enum**   | ['PWN', 'RE', 'Melware Development'] |
| **trim**   | true, false |
| **maxlength**   | 50, [100, 'error message'] |
| **minlength**   | 50, [100, 'error message'] |
| **match**   | regex, [regex, 'error message'] |
| **ObjectId**   | mongoose.Schema.Types.ObjectId |

### Model Methods
- create(object)
- find({...})
- findById(id)
- findByIdAndUpdate(id, {new data}, {new: true, runValidators: true})
- findByIdAndDelete(id)
### Model Hooks
```js
// uses normal function syntax to use the this keyword in the appropriate way
ModelSchema.pre('save', function(next) {
  this.slug = slugify(this.name, { lower: true })
  next() // must call next
})
```

<br/>
<details><summary>Show full example</summary>
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
<br/>
<br/>

## Body parser
> Parse the json data that comes from requests into javascript

> In `server.js`
```js
app.use(express.json())
```
<br/>
<br/>

## Custom error handling
> Inside `middlewares` folder, `errorHandler.js`
```js
const errorHandler = (err, req, res, next) => {
    const statusCode = res.statusCode === 200 ? 500 : res.statusCode
    res.status(statusCode).json({
        message: err.message,
        stack: process.env.NODE_ENV === 'production' ? null : err.stack
    })
}

const notFound = (req, res, next) => {
    const error = new Error(`Not Found ${req.originalUrl}`)
    res.status(404)
    next(error)
}

module.exports = {
    errorHandler,
    notFound,
}
```
> To use it, inside the controllers just throw new Error('error message')<br/>
> Include and `app.use` both methods in `server.js`
<br/>
<br/>

## Geocoding
> Install `node-geocoder`

> Add `GEOCODER_PROVIDER` and `GEOCODER_API_KEY` to the `.env` file

> Add the utility function
```js
const NodeGeocoder = require('node-geocoder')

const options = {
    provider: process.env.GEOCODER_PROVIDER,
    httpAdapter: 'https',
    apiKey: process.env.GEOCODER_API_KEY,
    formatter: null,
}

const geocoder = NodeGeocoder(options)

module.exports = geocoder
```
> Add the model hook on pre save
```js
// Geocode & create lcoation field
ModelSchema.pre('save', async function(next) {
  const loc = await geocoder.geocode(this.address)

  // GeoJSON object
  this.location = {
    type: 'Point',
    coordinates: [loc[0].longitude, loc[0].latitude],
    formattedAddress: loc[0].formattedAddress,
    street: loc[0].streetName,
    city: loc[0].city,
    state: loc[0].stateCode,
    zipcode: loc[0].zipcode,
    country: loc[0].countryCode,
  }

  // Do not save address in DB, we have the location
  this.address = undefined

  next()
})
```
### Application
> Get the zip code and distance

> Geocode using the zipcode

> Get the radius by dividing the distance by earth radius

> Query for the location field using `$geoWithin`
```js
    // route : /api/v1/bootcamps/:zipcode/:distance
    const { zipcode, distance } = req.params

    // Get lat/lng from the geocoder
    const loc = await geocoder.geocode(zipcode)
    const lat = loc[0].latitude
    const lng = loc[0].longitude

    // Calculate radius using radians : Devide distance by radius of earth
    // Earth radius : 6378km
    const radius = Number(distance) / 6378

    const bootcamps = await Bootcamp.find({
        location: {
            $geoWithin: {
                $centerSphere: [ [lng, lat], radius ]
            }
        }
    })
```
<br/>
<br/>

## Advanced Filtering

### Selecting certain documents
> Create a queryStr which holds the stringified version on `req.query`

> Add $ to the usual querying options

> Parse the querying string and run a query

```js
let query

// copy of req.query
let reqQuery = { ...req.query }

// exclude special-meaning fields
const excluded = ['select', 'sort']
excluded.forEach(param => delete reqQuery[param])

let queryStr = JSON.stringify(reqQuery)

queryStr = queryStr.replace(/\b(gt|gte|lt|lte|in)\b/, match => `$${match}`)

query = Model.find(JSON.parse(queryStr))

const bootcamps = await query
```
### Selecting certain fields within documents
```js
if (req.query.select) {
    const fields = req.query.select.split(',').join(' ')
    query = query.select(fields)
}
```
### Sort by certain fields
```js
if (req.query.sort) {
    const sortBy = req.query.sort.split(',').join(' ')
    query = query.sort(sortBy)
} else {
    query = query.sort('-createdAt')
}
```
### pagination
```js
const page = parseInt(req.query.page, 10) || 1
const limit = parseInt(req.query.limit, 10) || 25
const startIndex = (page - 1) * limit
const endIndex = page * limit
const total = await Model.countDocuments()

query = query.skip(startIndex).limit(limit)

// run the query

const pagination = {}

if (startIndex > 0) {
    pagination.prev = {
        page: page - 1,
        limit,
    }
}

if (endIndex < total) {
    pagination.next = {
        page: page + 1,
        limit,
    }
}

// add pagination object to the response
```
<br/>
<br/>

## Merging Routes
> Let's say we have these two routes: `/api/v1/courses` and `/api/v1/bootcamps/:bootcampId/courses`, we can use one controller method for both of them.

> In the courses routes file, it's simple

> In the bootcamps routes file, we require the `coursesRoutes` file, then
```js
router.use('/:bootcampId/courses', courseRoutes)
```

> In the `courseRoutes` we merge the routes
```js
const router = express.Router({ mergeParams: true })
```
> Then the methods on the `/` route in `courseRoutes` will be executed
> The controller method
```js
    let query

    if (req.params.bootcampId) {
        query = Course.find({ bootcamp: req.params.bootcampId })
    } else {
        query = Course.find()
    }

    const courses = await query

    res.status(200).json({
        success: true,
        count: courses.length,
        data: courses,
    })
```
> Also if we wanted to create a course for a specific bootcamp, we can use the same route with a `POST` method
```js
    req.body.bootcampId = req.params.bootcampId

    // Make sure that the bootcamp exists before create a course associated with it
    const bootcamp = await Bootcamp.findById(req.params.bootcampId)

    if (bootcamp) {
        const course = await Course.create(req.body)
        
        res.status(200).json({
            success: true,
            data: course,
        })
    } else {
        res.status(404)
        throw new Error('Bootcamp not found')
    }
```
<br/>
<br/>

## Populate
> .populate('fieled')
> .populate({ path: 'field', select: 'fields' })
```js
    query = Course.find().populate({
            path: 'bootcamp',
            select: 'name description',
        })
```
<br/>
<br/>

## Virtuals
> Virtuals are document proprties that you can get and set but that do not get presisted to MongoDB

> In the `big` Model(the one that will contain sub documents) we add with `timestamps`

```js
toJSON: { virtuals: true }
toObject: { virtuals: true }
```
> And we add the viruals
```js
BootcampSchema.virtual('courses', {
  ref: 'Course',
  localField: '_id',
  foreignField: 'bootcamp',
  justOne: false,
})
```
> Then in the `big` model's controller, we populate the query with `courses`
```js
query = Bootcamp.find(JSON.parse(queryStr)).populate('courses')
```
<br/>
<br/>

## Cascade-Delete Documnets
> Add a pre remove hook in the main model
```js
// Cascade Delete courses when a bootcamp is deleted
BootcampSchema.pre('remove', async function(next) {
  await this.model('Course').deleteMany({ bootcamp: this._id })
  next()
})
```

> In the controller of the main model, don't use `findByIdAndDelete()`, instead use `findById()` and then `document.remove()`
<br/>
<br/>

## Aggregation
```js
// Static method to get average of course tuitions
CourseSchema.statics.getAverageCost = async function(bootcampId) {
    const obj = await this.aggregate([
        {
            $match: { bootcamp: bootcampId }
        },
        {
            $group: {
                _id: '$bootcamp',
                averageCost: { $avg: 'tuition' }
            }
        }
    ])

    try {
        await this.model('Bootcamp').findByIdAndUpdate(bootcampId, {
            averageCost: Math.ceil(obj[0].averageCost / 10) * 10
        })
    } catch (error) {
        console.error(error)
    }
}

// Call getAverageCost after save
CourseSchema.post('save', function() {
    this.constructor.getAverageCost(this.bootcamp)
})

// Call getAverageCost before remove
CourseSchema.pre('remove', function() {
    this.constructor.getAverageCost(this.bootcamp)
})

```
<br/>
<br/>

## Image Upload
### Upload image locally on the server
> Install `multer`
```js
const express = require('express')
const multer = require('multer')
const path = require('path')

const router = express.Router()

const checkFileType = (file, cb) => {
    const fileTypes = /jpg|jpeg|png/
    const extname = fileTypes.test(path.extname(file.originalname).toLowerCase())
    const mimetype  = fileTypes.test(file.mimetype)

    if (extname && mimetype) {
        return cb(null, true)
    } else {
        cb('Images only!')
    }
}

const storage = multer.diskStorage({
    destination(req, file, cb) {
        cb(null, 'uploads/')
    },
    filename(req, file, cb) {
        cb(null, `${file.fieldname}-${Date.now()}${path.extname(file.originalname)}`)
    }
})

const upload = multer({
    storage,
    fileFilter: function(req, file, cb) {
        checkFileType(file, cb)
    },
})

router.post('/', upload.single('image'), (req, res) => {
    res.send(`\\${req.file.path}`)
})

module.exports = router
```
### Upload image to Cloudinary
> Install `multer`

> Install `cloudinary`

> Install `multer-storage-cloudinary`
```js
const express = require('express')
const cloudinary = require("cloudinary").v2;
const { CloudinaryStorage } = require("multer-storage-cloudinary");
const multer = require('multer')

const router = express.Router()

cloudinary.config({
    cloud_name: "dfnqmhmae",
    api_key: "766665794797967",
    api_secret: "Bm0BfN4p2Rlsn2_3RQZQKqWnDp4",
})

const storage = new CloudinaryStorage({
    cloudinary: cloudinary,
    params: {
      folder: "users_photos",
    },
})

const upload = multer({
    storage: storage,
})

router.post('/', upload.single('image'), (req, res) => {
    res.send(req.file.path)
})

module.exports = router
```
<br/>
<br/>

## Authentication
### Packages
> `jsonwebtoken` and `bcryptjs`

### File Structure

> **Route:** /api/v1/auth

> `authControllers` and `authRoutes` files

### Password Encryption
> In the `User` model
```js
// Encrypt password
UserSchema.pre('save', async function(next) {
    if (!this.isModified('password')) {
        next()
    }

    const salt = await bcrypt.genSalt(10)
    this.password = await bcrypt.hash(this.password, salt)
}) 
```

### User Registration
```js
// @desc        Register user
// @router      POST /api/v1/auth/register
// @access      Public
exports.registerUser = asyncHandler(async (req, res) => {
    const { name, email, password, role } = req.body

    const emailExists = await User.findOne({email})

    if (emailExists) {
        res.status(400)
        throw new Error('User already exists')
    }

    // Create user
    const user = await User.create({
        name,
        email,
        password,
        role
    })

    if (user) {
        res.status(200).json({success: true, data: user})
    } else {
       res.status(422)
       throw new Error('Invalid Input')
    }
})
```
 

<br/>
<br/>
<br/>
<br/>
<br/>

## Data Seeder Script
> configurate the `.env` file inside the seeding script

> connect to the database

> Import the models

> Add `exportData` and `destroyData` functions

> Configurate the `process.argv[2]` options

```js
const fs = require('fs')
const path = require('path')
const mongoose = require('mongoose')
require('dotenv').config()
require('colors')

// Load models
const Bootcamp = require('./models/Bootcamp')

// Connect to db
const connect = async() => {
    try {
        await mongoose.connect(process.env.MONGO_URI)
    } catch (error) {
        console.log(error)
    }
}

connect()


// Read JSON files
const bootcamps = JSON.parse(fs.readFileSync(path.join(process.cwd(), 'data', 'bootcamps.json'), 'utf-8'))

// Export data into DB
const importData = async() => {
    try {
        await Bootcamp.create(bootcamps)

        console.log('Data Exported Successfully'.green.inverse);
    } catch (error) {
        console.error(error)
    }
}

// Delete Data from DB
const deleteData = async() => {
    try {
        await Bootcamp.deleteMany()

        console.log('Data Destroyed Successfully'.red.inverse);
    } catch (error) {
        console.error(error)
    }
}

// Commands
if (process.argv[2] === '-e') {
    importData()
} else if (process.argv[2] === '-d') {
    deleteData()
}
```
