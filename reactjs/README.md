# Crashcourse React.js


## Introduction

React adviseert om vooral functions te gebruiken ipv classes. De reden is omdat een class die hetzelfde doet als een functie, veel meer gegenereerde JavaScript code heeft. Om states te gebruiken in functies, maken we gebruik van useState.

### DOM
DOM stands for Document Object Model, it's what the browser uses to display a website/webapp. It has an API too access elements of the website. With JavaScript, like JQuery, you can manipulate the DOM through the API (like getElementWithTag, findElementWithClassName...). This manipulation using DOM-api is also called **Imperative** paradigm.


### Imperative
Imperative is the behaviour of directly changing elements. For example, if a user has logged out, the program than change button's name, than shows a message, than change nabar background color, than... etc. This gets complex if a lot things are dependent on each other.
So, it directly executes the steps based on certain conditions.
For example:

```js
if(user === 'logged out') {
	// change button name
	getElementByTag('login').setText('Login');
	getElementByTag('login').setBackground('blue');
	... and so on;
}
```

### Declarative
Is like a set of rules (state) how the website or app should behave. If the set of rules has changed, the changes are than applied to rendering accordingly. 

For a clearer example go to: [imerative vs declarative](https://codeburst.io/declarative-vs-imperative-programming-a8a7c93d9ad2)


## React Concepts
1. Don't touch the DOM, React does it (with **VirtualDOM** in case of webapps).
2. Build websites like lego blocks (called **Components**).
3. Unidirectional data flow (changes of state trickles down to other components like pyramide, so you have like one big app state on the top).
4. UI done by React, the rest is up to you (UI rendering is taking care of by React, the logic is up to you). React framework can be used by Android/iOS, Desktop and so on... It just needs a different renderer, the concept stays the same, which is state/props and components.


## React Basics

Creating a React app, do the following:
```npx create-react-app foldername```

This will download all the necessary files to run a basic react app. In ```package.json``` you have some scripts you can run, namely:

* start
* build
* test
* eject

#### start
Running ```npm start``` will run react app which is accessible with from the browser on the default port 3000: ```http:\\localhost:3000``` We use this mainly for development.

#### build
This script will create a build folder with optimized code from the src folder. When it's build, the files can than be copied to a server for production. So it's used for deployment to production environment.

#### test
Well, this is for testing to use with Jest for example.

#### eject
It's not necessary to use this, because that's more for developers who improve React.


### Babel
Converts jsx files in plain JavaScript and makes sure it runs on popular webbrowsers like Firefox, Chrome, Safari and Edge.

### Webpack
Is a bundler to bundle all the JavaScript files into one or two optimized files.


## React modules

### React

```react``` is used to write HTML like syntaxes and contains modules like Component we can derive from.


### ReactDOM

```react-dom``` is to render the changes to DOM. So, it's actually the VirtualDOM that is doing the rendering.


## React Component

### Class vs Function

Classes are bigger with much more overhead, while Function is smaller and lighter. That's why we see more functions than classes.


## React State

To change a state, it must be done through ```this.setState({ name: 'Bilal' })```

The reason for this is, React needs to know something has changed. ```setState``` notifies React for a change. If state is directly modified like ```this.state.name = 'Malik'``` React still won't know something has changed and so the change won't be rendered to the DOM.

## React props
React props are the properties of an element, for example:

```
function Component() {
	return <MyElement name="Kareem" message={message} />;
}
```

```name``` and ```message``` are properties passed to ```<MyElement>```.

To access the element can be doen like below:

```
function MyElement(props) {
	const {name, message} = props;
}
```

As you see in the code the properties can be accessed from the ```props``` object. The way we accessed is done with destructering using ```{}```.

The same can be achieved as below:


```
function MyElement(props) {
	const name = props.name
	const message = props.message;
}
```

## Import, Export

### Export
To export a function component there are 2 ways to do it, namely:

* Named export
* Default export

```
// Named export
export function LogIn() {
	return ...
}

export function LogOut() {
	return ...
}

//...

// 
export default function Register() {
	return ...
}
```

### Import
To import the 2 functions above, we do as following:

```
// Named import
import { LogIn, LogOut } from "./Login.js";

// Default import
import Register from "./Register.js";
```


## React Hooks

### useState

Met useState wordt een status van een variabele bijgehouden door React. Als deze gewijzigd is, dan wordt de view die de status heeft, gerenderd.
Hieronder een voorbeeld:

```js
import { useState } from 'react';

// ...

const Incrementer = () => {

	const [count, setCount] = useState(1);
	
	const handleBtnAdd = (event) => {
		setCount(state+1); // vertelt de VirtualDOM dat er gerenderd moet worden, want de count state is gewijzigd
	}
	
	return (
		<div> {count} </div>
	)
}

```

Wanneer je useState(1) aanroept, dan kun je een initiele waarde meegeven, in dit geval een 1. Het retourneert eigenlijk een array element [0] de state variabele en element[1] een functie om de state te updaten, daarom zetten we een 'set' ervoor. Het wijzigen van de count variabele mag alleen via setCount(), omdat behind the scene de ReactDOM dan weet welk gedeelte opnieuw gerenderd moet worden.


### useEffect
Met UseEffect kunnen kun je een stukje code uitvoeren als een bepaalde variabele gewijzig is. Dus ipv renderen, voert het een stukje code uit.

Zie onderstaande code:

```
// 1. we monitor logout state
const [logout, setLogout] = useState(false);

// 2. when logout state changes, we execute the passed function
useEffect(() => {
	if(logout) {
		console.log("You're logged out!);
	}
	
	// 3. if this component component will be destroyed, execute this cleanup()
	return function cleanup() => {
		redirect("/login");
	}
	// 4. monitor logout state
}, logout);

//...

setLogout(true);

```

As we see, from point 2 we add an effect to do something if logout variable has changed. We keep monitoring logout variable adding the variable in point 4. This can be any variable, an object, an array or whatever.

If we add also a return statement like in point 3, than this will be called when the functional component is removed or destroyed by react. This way you can do some cleanup like releasing memory or unsubscribe from a publisher or something.


### useRef()

Met useRef kun je elementen manipuleren zonder een rerender te triggeren. Dus React zal niet reageren om een rerender uit te voeren. React is namelijk zo gemaakt dat als er een wijziging is in de state, dat een rerender getriggerd wordt op een efficiente manier. Maar er zijn situaties waarbij je dat wilt voorkomen (zoek voorbeelden wanneer te gebruiken).
useRef kun je vergelijken met jQuery waarbij je direct een DOM-element aanpast. Het is dus niets anders dan direct een element aanpassen, zonder een rerender van React.


```
const UseRefExample = () => {

	// returns an object like: {current: null}
	const refContainer = useRef(null);
	
	const handleSubmit = () => {
		e.preventDefault();
	}
	
	return(
		<>
			<form className="form" onSubmit={handleSubmit}>
				<div>
					<input type="text" ref={refContainer} />
					<input type="submit">Submit</button>
				</div>
			</form>
		</>
	);
	
}
```



# Redux

## Introduction

Redux is een soort singleton met een subscriber/publisher concept. Je kan je registreren voor een bepaalde state, als de state gewijzigd is, krijg je een update van.

## Concept

Redux bestaat uit de volgende belangrijke componenten:

- **Store**: Daarin worden alle states bewaard
- **Action**: De naam van een actie, maar doet verder niks
- **Reducer**: Voert een bepaalde actie uit a.h.v. action type en zorgt daarmee dat de state gewijzigd wordt
- **Dispatch**: Triggert de action die vervolgens de reducer aan het werk zet

Verder heb je ook:

- **Connect**: Daarmee kun je een component connecten met redux als deze niet wrapped is met Provider (van redux)
- **mapStateToProps**: Een methode om state in redux store te mappen naar component's props

###Example
Laten we een action maken:

```js
// This is an action with a type named 'LOGIN'
const login = () => {
    return {
        type: "LOGIN"
    }
}

const logout = () => {
    return {
        type: "LOGOUT"
    }
}
```

Vervolgens maken we een reducer:

```js
const authenticateReducer = (state=false, action) => {
    switch(action.type) {
        case 'LOGIN': return true;
        case 'LOGOUT': return false;
        default: return state;
    }
}
// Let op! state mag ook een uitgebreide object zijn!
```

En uiteindelijk hebben we een redux store nodig, om de reducer in de store te zetten:

```js
import { createStore } from 'react-redux';

const store = createStore(authenticateReducer);
```

Waarde uitlezen mbv useSelector:

```js
import { useSelector } from "react-redux";

const authenticated = useSelector((state) => state.authenticateReducer);

console.log("User is ", authenticated?"logged in":"logged out");
```

For the change take effect, we need to dispatch the new value:

```js
import { useDispatch } from "react-redux";

function SignIn() {
    const dispatch = useDispatch();
    //...
    dispatch(login()); // this will cause redux to change state
}
```
