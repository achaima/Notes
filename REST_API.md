#What is a REST API


API:

- **A**pplication **P**rogramming **I**nterface

REST:

- **RE**presresetational **S**tate **T**ranfer


##REST

- Architectual style of the web
- A standard / set of guidelines by which we can structure and create API's
- REST API's have identifiable properties

The make use of resource-based URL's

**Webpage** - 
http://www.football.com/scores
http://www.football.com/teams/ arsenal

**JSON** - 
http://www.football.com/api/scores
http://www.football.com/teams/ arsenal
e.g.

	"scores": {
		"team":"arsenal",
		"for": 10,
		"against": 50,
		"wins": 1,
		"losses": 2,
		"draws":0
	}
	
	
###They Make use of HTTP Methods

1. GET - Retrieve data from the server
2. POST - Send data to the server
3. PUT/PATCH - Updata data
4. DELETE - Delete data


###They make use of HTTP Status codes

Examples:

- 200 - OK
- 404 - Resource not found
- 500 - Internal Server Error


##HTTP Methods & API Routes

> HTTP Methods

A way of telling the server what type of request you are making


###Our API Routes

Read/ retrieve a list of ninjas:

- GET request to http://www.ninjago.com/api/ninjas

Create a new ninja:

- POST request to http://www.ninjago.com/api/ninjas

Update an existing ninja:

- PUT request to http://www.ninjago.com/api/ninjas/id

Delete a ninja:

- DELETE request to http://www.ninjago.com/api/ninjas/id

##Creating an Express App

npm install express --save 

```js
const express = require('express');

//set up express app
const app = express();

//assigning PORT number
const PORT = process.env.port || 3000;

//listen for requests
app.listen(PORT, function(){
  console.log('app is listening on port', PORT);
});

```

##Handling Requests in Express

We can start to set up our routes and our route handlers

When we created an app variable by setting it to express the function it handed over to us a load of http methods (GPPD) that we can use on it to listen for such requests

##Creating Routes

We are going to be making routes using the express class routes

This allows us to make modular routes, keeping it organised

```js
const express = require('express');
const router = express.Router();
var guestController = require('../controllers/guest.controller');

router.get('/guests', guestController.getGuest);

router.post('/guests', guestController.createGuest);

router.put('/guests', guestController.updateGuest);

router.delete('/guests', guestController.deleteGuest);

module.exports = router;
```

* We need to export the routes and use them in server.js

* So now we mounted all our routes on router and now we export it

```js
module.exports = router;
```

Import it into server file

server.js

```js
const express = require('express');

//set up express app
const app = express();
const router = require('./api/config/router');

//assigning PORT number
const PORT = process.env.port || 3000;

//listen for requests
app.listen(PORT, function(){
  console.log('app is listening on port', PORT);
});

```

Now how we do say to our express app that we want to use those routes?

* We can use a method called ```use``` available on our app

 
```js
const express = require('express');

//set up express app
const app = express();
const router = require('./api/config/router');

//assigning PORT number
const PORT = process.env.port || 3000;

//initialise routes
app.use('api',routes); <----

//listen for requests
app.listen(PORT, function(){
  console.log('app is listening on port', PORT);
});

```

* This ```use``` method we use in our app when we want to use middleware
* Middleware is code that runs between the request and the response

##Handling POST Requests & middleware

* Body_parser is middleware

insert picture

* We have req coming which hits the express app
* The express app looks at the type of request coming in hands it to the correct ```Route Handler``` and that does something with the request and sends a response out
* The Route Handler code is fired between req coming in and res going out any code that between them is known as middleware
* We use this middleware by doing ```app.use``` and adding the middleware we want to use

so if we added more middleware
we use this middleware by doing app.use and doing the middleware we want to use

insert 3 types of middleware


What if we use the Body Parser?

* Body parser is going to look at the body of the request. Its going to take, to parse it and attach it to the request object
* Remember in these route handlers we get access to that request object so by the time that request object gets to the Route handler we have access to that data from the body because body Parser has attached it to the request object
* Order is very important otherwise if body parser was after we won't have access


```js
const express = require('express');

// set up express app
const app = express();
const bodyParser = require('body-parser');
const router = require('./api/config/router');
const PORT = process.env.port || 3000;


app.use(bodyParser.json());
app.use(bodyParser.urlencoded({ extended: true}));

//Ininitialise routes
app.use(router);

// listen for requests
app.listen(PORT, function(){
  console.log('app is listening on port', PORT);
});

module.exports = app;
```

#Models and Schemas

##Models

- Models represent collections in MongoDB

## Schemas

- Schemas define the structure of our data objects

		{
		 name: String,
		 rank: String,
		 availability: Boolean
		 }
		 
##Mongoose


- Add a layer of methods to easily save, edit, retrieve and delete data from mongodb

- Allows us to create our Models and Schemas easily using mongoose and its methods 

- We use mongoose and its methods to create Model and Schema


```js
const mongoose = require('mongoose');
const Schema = mongoose.Schema;

const GuestSchema = ({
  firstName: {
    type: String,
    required: [true, 'First name field is required']
  },
  lastName: {
    type: String,
    required: [true, 'Last name field is required']
  },
  extraGuests: {
    type: Number,
    required: [true, 'This field is required']
  },
  attendingEvents: {
    type: String,
    required: [true, 'This field is required']
  },
  comments: {
    type: String,
    required: false
  }
});

const weddingGuests = mongoose.model('Guests', GuestSchema);
module.exports = weddingGuests;
```

##Saving data to mongoDB

1. Have to make sure mongodb is running in the background in terminal
2. We need to connect to it so we need to set the app to do that

```js
const express = require('express');
const bodyParser = require('body-parser');
const mongoose = require('mongoose'); <--

//set up express app
const app = express();


const router = require('./api/config/router');
const MONGODB_URI = process.env.MONGODB_URI || 'mongodb://localhost/wedding-project';
const PORT = process.env.port || 3000;

mongoose.connect(MONGODB_URI, function (err) {
  if (err) {
    console.error('Could not connect to Mongo: err:', err);
    process.exit(1);
  }
  console.log('Connected to database:', mongoose.connection.name);
}); <--



app.use(bodyParser.json());

//initialise routes
app.use(router);

//listen for requests
app.listen(PORT, function(){
  console.log('app is listening on port', PORT);
});

module.exports = app;
```
Adding ni

```js
const express = require('express');
const router = express.Router();
const Ninja = require('../models/ninja'); <-- //Now we can use the ninja model in this file

// get a list of ninjas from the db

router.get('/ninjas', function(req, res){
	res.send({type: GET});
});
	
router.post)'/ninjas', function(req, res){
  	var ninja = new Ninja(req.body) <-- // This is creating a new record, created locally not sent to mongodb just yet
  	ninja.save(); 
  	res.send({
  	type: 'POST'
  	name: req.body.name,
  	rank: req.body.rank
  });
 });
```
In more detail:

```js
var ninja = new Ninja(req.body) 
```
1. Creating a new ninja
2. We need to give this instance/ninja some data so we put in req.body, which is data sent to us in the body of the request
3. Now we can save it to the database

```js
ninja.save(); 
```

However, instead of creating a new instance and saving it the db we can make it shorter using Ninja```.create```  on the model

```js
Ninja.create(req.body);
```

Full picture


```js
const express = require('express');
const router = express.Router();
const Ninja = require('../models/ninja'); <-- //Now we can use the ninja model in this file

// get a list of ninjas from the db

router.get('/ninjas', function(req, res){
	res.send({type: GET});
});
	
router.post)'/ninjas', function(req, res){
  	Ninja.create(req.body); 
  	res.send({
  	type: 'POST'
  	name: req.body.name,
  	rank: req.body.rank
  });
 });
```

## PUT REQUESTS

We use it to update existing data stored in the database

```js
//update a ninja in th db
 
router.put('/ninjas/:id', function(req,res, next) {
	Ninja.findByIdAndUpdate({_id: req.param.id}, req.body).then(function(){
		Ninja.finOne({_id: req.params.id}).then(function(ninja){
 		 res.send(ninja);
    });
}):


```