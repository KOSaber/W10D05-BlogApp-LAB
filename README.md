[![General Assembly Logo](https://camo.githubusercontent.com/1a91b05b8f4d44b5bbfb83abac2b0996d8e26c92/687474703a2f2f692e696d6775722e636f6d2f6b6538555354712e706e67)](https://generalassemb.ly/education/web-development-immersive)
![Misk Logo](https://i.ibb.co/KmXhJbm/Webp-net-resizeimage-1.png)

# LAB - BlogApp

## Step 1 - Initial setup

1.	mkdir blog-app-express
2.	cd blog-app-express
3.	npm init
4.	Add description: “Blog Express App”
5.	Set entry point: server.js
6.	Code . to open it in visual studio
7.	npm install express --save
8.	touch server.js

```javascript
// Require necessary NPM Packages
const express = require('express');

// Instantiate Express Application Object
const app = express();
app.use(express.json());

// Define PORT for API to run on
const port = process.env.PORT || 7000;

// Start the server to listen for requests on a given port
app.listen(port, function() {
  console.log(`Blog Express App is listening on port ${port}`);
});
```

## Step 2 - Complete Basic App Functionality

1.	Create app folder in root directory.
2.	Then create models, routes folder inside app folder.
3.	Create config folder in root directory.
4.	Create index.js in routes folder.

```javascript

    // Require necessary NPM Packages
    const express = require('express');

    // Instantiate a Router (mini app that only handles routes)
    const router = express.Router();

    /**
     * Action:      INDEX
     * Method:      GET
     * URI:         /
     * Description: Get the Root Route
     */
    router.get('/', function(req, res) {
      res.json({message: 'Welcome to Blog Express App'});
    });


    // Export the Router so we can use it in the server.js file
    module.exports = router;
```

5.	In order to view root welcome page, add index routes in server.js file.

```javascript
const indexRouter = require('./app/routes/index');
app.use(indexRouter);
```

## Step 3 - Database Setup

1. Exit server and install mongoose “npm install mongoose”.

2. Add require mongoose statement in server.js

```javascript
const mongoose = require('mongoose');
```

3. Create db.js file in config folder.

```javascript
'use strict';

// Creating a base name for the MongoDB
const mongooseBaseName = 'blogapp'

// Create the MongoDB URI for Development and Test
const database = {
  development: `mongodb://localhost/${mongooseBaseName}-development`,
  test: `mongodb://localhost/${mongooseBaseName}-test`
}

// Identify if development environment is Test or Development
// select DB based on whether a test file was executed before `server.js`
const localDB = process.env.TESTENV ? database.test : database.development

// Environment variable MONGODB_URI will be available in
// Heroku production environment otherwise use Test or Development DB
const currentDB = process.env.MONGODB_URI || localDB

// Export the appropriate database based on the current environment
module.exports = currentDB;
```

4.	Require database configurations in server.js and establish a connection.

```javascript
const db = require('./config/db');

// Establish Database Connection
mongoose.connect(db, {useNewUrlParser: true});
mongoose.connection.once('open', function() {
  console.log('connected to mongo');
});
```

## Step 4 - Creating Article Model

1.	Create article.js file in models folder for schema.

```javascript
// Require necessary NPM Packages
const mongoose = require('mongoose');

// Define Article Schema
const articleSchema = new mongoose.Schema({
  title: { type: String, required: true },
  content: String,
  author: { type: String, required: true },
  publishedOn: { type: Date, default: Date.now },
  published: { type: Boolean, default: true },
}, {
  timestamps: true,
});

// Compile our Model based on the Schema
const Article = mongoose.model('Article', articleSchema);

// Export our Model for use
module.exports = Article;
```

## Step 5 - Creating Article Routes

1.	Create articles.js file in routes folder.
```javascript
// Require necessary NPM Packages
const express = require('express');

// Require Mongoose Model for Article
const Article = require('../models/article');

// Instantiate a Router (mini app that only handles routes)
const router = express.Router();

// Middleware required for post
router.use(express.urlencoded());


/**
 * Action:      INDEX
 * Method:      GET
 * URI:         /api/articles
 * Description: Get All Articles
 */
router.get('/api/articles', function(req, res) {
  Article.find()
    // Return all Articles as an Array
    .then(function(articles) {
      res.status(200).json({ articles: articles });
    })
    // Catch any errors that might occur
    .catch(function(error) {
      res.status(500).json({ error: error });
    });
});


/**
 * Action:      CREATE
 * Method:      POST
 * URI:         /api/articles
 * Description: Create a new Article
 */
router.post('/api/articles', function(req, res) {
  console.log("coming");
  console.log(req.body.title);
  Article.create(req.body)
    // On a successful `create` action, respond with the 201
    // HTTP status and the content of the new article.
    .then(function(newArticle) {
      res.status(201).json({ article: newArticle });
    })
    // Catch any errors that might occur
    .catch(function(error) {
      res.status(500).json({ error: error });
    });
});



// Export the Router so we can use it in the server.js file
module.exports = router;
```

2.	Add routes to server.js file

```javascript
const articlesRouter = require('./app/routes/articles');
app.use(articlesRouter);
```

## Step 6 - Check your app

1.	Check browser/ postman for read API. (this will give you empty array at first).
2.	Connect postman to post 20 different articles into database.
3.	Remember to modify/ add key “content-type” and value “application/json” in header of postman.


## Step 7 - Complete your CRUD Operations
<hr>

1. Create an API route to read a single article.
2. Create Update and Delete APIs in the same application.

<hr>
