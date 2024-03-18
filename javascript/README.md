# JavaScript Crash Course

## 

* Object literals
* Function objects
* Object constructors
* prototype
* ...



## Everything is an Object

Most developers coming from OOP languages like C++ or Java get confused why almost everything in JavaScript is an object.

An object is nothing more than a collection of key/value pairs.

For example, a function is an object, look at the code below:

```js
function say() {
  console.log('Yo D!');
}

say(); // output: Yo D!

// we can also use say() as object:
say.message = 'Yo Wazup!';
console.log(say.message'); // output: Yo Wazup!
```

As you can see, you can add to ```say``` function new properties, which is actually like adding a new key/value pair. So, the function is not like a real function, but it's an object that can also act as a function.

### Object literals
In Javascript an object literal is comparable to a **dictionary** or a **hashmap** in other languages, or more in general a **collection of key/value pairs** and you write it like below:

```js
let myObject = {
    sProp: 'some string value',
    numProp: 2,
    bProp: false
};

// or

let Swapper = {
    // an array literal
    images: ["smile.gif", "grim.gif", "frown.gif", "bomb.gif"],
    pos: { // nested object literal
        x: 40,
        y: 300
    },
    onSwap: function() { // function
        console.log('Hello there');
    }
};
```
In Javascript you can even add a function as object to a property, like ```onSwap```. 


### Protoype function/object
In JavaScript, when we create a function, JavaScript engine automatically adds a prototype property inside the function object. On this ProtoType property or object, we can attach properties or other functions to it. This way, other derived objects will inherit those ProtoType properties and functions.

Let's anaylyse the snippet below:

```js
// function constructor 
function Person(name, job, yearOfBirth){ 
	this.name = name; 
	this.job = job; 
	this.yearOfBirth= yearOfBirth; 
} 
// this will show Person's prototype property. 
console.log(Person.prototype); 
```

This result in the following:

![](prototype_object.png)



Prototype functions are functions that are inherited from a parent object, often called Prototype object. Yes, it's a bit confusing. Ok, an object is actually just a bunch of key/value pairs, like a NSDictionary in Objective-C or HashMap in Java. But to have some more default functions, like Java's `toString()` method, an object has an Object prototype. This ProtoType has some default methods/properties, like `toValue()`




### Function() Constructor
We can create a function with ```new Function() ```, which is called Function Contructor.
Check out the example below:

```js
let add = new Function("a", "b", "return a * b");

let x = add(4, 3);

//===============================================
// which is the same as:
//===============================================
let addFunction = function (a, b) {return a * b};

let x = addFunction(4, 3);
```
The latter method is what we normally do, but under the hood, the first method is done.

### Factory function

```js
function createCircle(radius) {
	return {
		radius,
		draw: function() {
		  			console.log('I draw a circle of radius: ' + this.radius);
		  			}
	}
}

let cricle = createCircle(12);
```

### Constructor function

```js
function Circle(radius) {
  this.radius = radius,
  this.draw = function () {
    			console.log('I draw a circle of radius: ' + this.radius);
  			  		}
}

let cricle = new Circle(12);
```


### Optionals
Optionals are like those in Swift language, it's like saying this property or object may be null, so take care.
For example:

```js
let user = {}; // user has no address

alert( user.address && user.address.street && user.address.street.name ); // undefined (no error)
```
As you see, the above code is ugly, with so many ```&&``` to meet the requirement. With optionals, we can solve it like below:

```js
let user = {}; // user has no address

alert( user?.address?.street ); // undefined (no error)
```
We can do this also with ```?.()``` or ```?.[]```:

```js
let userAdmin = {
  admin() {
    alert("I am admin");
  }
};

let userGuest = {};

userAdmin.admin?.(); // I am admin

userGuest.admin?.(); // nothing (no such method)
```

```js
let user1 = {
  firstName: "John"
};

let user2 = null; // Imagine, we couldn't authorize the user

let key = "firstName";

alert( user1?.[key] ); // John
alert( user2?.[key] ); // undefined

alert( user1?.[key]?.something?.not?.existing); // undefined
```



### Getters and Setters



### Spread operator
Converts an array of elements into single elements each. Confusing huh? Well, here's an example below:

```js
// normal array concat() method 
let arr = [1,2,3]; 
let arr2 = [4,5]; 

arr = arr.concat(arr2); 

console.log(arr); // [ 1, 2, 3, 4, 5 ] 


// spread operator doing the concat job 
let arr = [1,2,3]; 
let arr2 = [4,5]; 

arr = [...arr,...arr2]; 
console.log(arr); // [ 1, 2, 3, 4, 5 ] 

```


### Higher Order functions

High Order functions are functions that adds functionality to, for example, an array. Arrays have functions like:

* map()
* forEach()
* filter()
* ...

These are special functions that iterate through an array, using a callback function. Below you'll find an example of forEach():

```js
let list = [obj1, obj2, obj3, ...];

list.forEach( obj => {
	console.log(obj);
});
```

forEach() generates a function for each iteration. This is a big difference with an old fashion for-loop:

```js
let list = [obj1, obj2, obj3, ...];

for( let i=0; i<list.length; i++ {
	const obj = list[i];
	console.log(obj);
}
```

The big difference is especially when using async/await. In a for-loop it will be synchronously, while in forEach() it will be asynchronously. The latter is because for each iteration a new function will be generated and called separately.


