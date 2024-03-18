# Crash crouse Node.js


## Javascript

## Patterns and techniques

* Cross Cutting Concerns

## NPM

![](https://camo.githubusercontent.com/e92779f59d5b86687b47e86a70d6a3c814cc3b75/68747470733a2f2f6e696d69742e696f2f696d616765732f6e2f6e2e676966)

### Installing NPM

#### Node version

`node -v`

#### n
**n** is a tool to upgrade Node.js, to install is run

```
npm install -g n
```

To see a list of installed Node version, simply type: `n`

To install a node version, there are several ways to do that:

```
n 10.16.0		# installs node version 10.16.0
n stable		# installs latest stable version
n lts			# installs long term service version
```

#### Remove node.js
Remove some cached versions:

```
n rm 0.9.4 v0.10.0
```

Removing all cached versions except the current version:

```
n prune
```

Remove the installed node and npm:

```
n uninstall
```

For more info, go to [Github](https://github.com/tj/n)



For installing **npm** you can go to: [npm install on ubuntu](https://attacomsian.com/blog/install-nodejs-npm-ubuntu)


To install packages for productions:

```
npm i -S
npm i --save-prod
npm i --save 	#sets default to production
```

To install packages for development:

```
npm i -D
npm i --save-dev
```

To install packages globally, for example nodemon:

```
npm  i nodemon -g
```

### Node project

#### Initialize node project

Initialize a node.js project

```
npm init -y
```
-y: answer all questions with yes


This creates a npm package.json describing the project.

#### Dependencies
In package.json are 2 dependencies:

* **production**: `dependencies`
* **development**: `devDependencies`

Packages for development are only used locally.


## Express.js


Installing Express.js framework

```sh
npm i express
```

### Must have packages

* **nodemon**: re-runs app after changing code
* **dotenv**: loads environment variables to process.env
* **config**: for configuration settings
* **joi**: validates input fields
* **morgan**: logs http requests
* **winston**: sophisticated logger
* **mongoose**: ODM (object document mappers, a kind of ORM) for MongoDB
* **underscore**: utility lib
* **bcrypt**: encryption/decryption
* **jsonwebtoken**: for creating jwt tokens

#### Server

Basic setup to listen to a port for requests:

```js
const express = require('express');
const app = express();
const config = require('config');
const bodyParser = require('body-parser');
const logger = require('morgan')


app.use(bodyParser.urlencoded({ extended: true }));
app.use(bodyParser.json());

app.use(logger('dev'));


app.get('/', (req, res) => {
    res.send('Let's make the world a peacful place!');
});


const port = process.env.LISTEN_PORT;
app.listen( port || 3210, _ => {
    console.log(`Backend app is running at port ${port}`);
});
```


## Mongoose.js
Here is a simple model schema:

```js
const mongoose = require("mongoose");
const bcrypt = require("bcrypt");
const jwt = require("jsonwebtoken");
const config = require("config");
const _ = require('underscore');
const Schema = mongoose.Schema;

SALT_WORK_FACTOR = 10;

const userSchema = new Schema(
	{
		email: {
			type: String,
			unique: true,
			required: true,
			trim: true,
		},
		username: {
			type: String,
			trim: true,
			default: ''
		},
		nickname: String,
		password: {
			type: String,
			required: true,
		},
		owner: {
			type: Boolean,
			default: false,
		},
	},
	{ timestamps: true }
);

// Hash password before saving to the database
userSchema.pre("save", function (next) {
	const user = this;

	// only hash the password if it has been modified (or is new)
	if (!user.isModified("password")) return next();

	// generate a salt
	bcrypt.genSalt(SALT_WORK_FACTOR, function (err, salt) {
		if (err) return next(err);

		// hash the password along with our new salt
		bcrypt.hash(user.password, salt, function (err, hash) {
			if (err) return next(err);

			// override the cleartext password with the hashed one
			user.password = hash;
			next();
		});
	});
});

/**
 * This method will only be used when registrating a new user.
 */
userSchema.methods.verifyPassword = function (candidatePassword, cb) {
	bcrypt.compare(candidatePassword, this.password, function (err, isMatch) {
		if (err) return cb(err);
		cb(null, isMatch);
	});
};

/**
 * This method will only be used authentication by
 * registration or email.
 */
userSchema.methods.generateJwtToken = function() {
	return jwt.sign(_.pick(this, '_id', 'username'), config.get('jwtPrivateKey'));
}

const User = mongoose.model("User", userSchema);

module.exports = User;

```

Here is an example restaurant schema:

```js
const mongoose = require("mongoose");
const category = require("./category");

let Schema = mongoose.Schema;

const restaurantSchema = new Schema(
	{
		name: {
			type: String,
			required: true,
			unique: true,
		},
		owner: {
			type: mongoose.Schema.Types.ObjectId,
			ref: "User",
			required: true,
		},
		phoneNumber: {
			type: String,
			required: true,
			unique: true,
		},
		address: {
			street: {
				type: String,
				required: true,
			},
			zipcode: {
				type: String,
				required: true,
			},
			city: {
				type: String,
				required: true,
			},
		},
		category: [
			{
				type: Schema.Types.ObjectId,
				ref: "Category",
			},
		],
	},
	{ timestamps: true }
);

// First argument is the name of the collection
const Restaurant = mongoose.model("Restaurant", restaurantSchema);

module.exports = Restaurant;
```

## Routes
We need to make our routes and folders well structured like below:

```
routes/
	api/
		v1/
			index.js
			user.js
			auth.js
			...other routes
```

To do this, we write the index.js file as below:

```js
const express = require("express");
const router = express.Router();

const user = require("./users");
const auth = require("./auth");

router.use("/user", user);
router.use("/auth", auth);

module.exports = router;
```

In app.js we define our routes like the example below:

```js
const express = require("express");
const app = express();
const api = require("./routes/api/v1/");

app.use("/api/v1", api);

// ...
```


## Error handling



