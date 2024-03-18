# Svelte carshcourse

## Get started
Om minimalistisch Svelte app te genereren typen we de volgende commando:

`
npm init vite
`

Je krijgt een lijst waaruit je moet selecteren (vanilla JS, react, vue, svelte, ...)
Wij selecteren gewoon Svelte, vervolgens kun je tussen JS of TS kiezen. Wij kiezen voor de studenten JS.


## React vs Svelte
Componenten in React hebben meestal de extensie **.jsx**. Daarin wordt html met JS gemixt. In Svelte heb je ook zoiets, maar dan met de extensienaam **.svelte**

In React is een component JS met in de return html code. Het is voor de beginner dan ook onwennig om te lezen. Verder wordt er veelvuldig gebruik gemaakt van useState() en useEffect hooks. Dit is voor de beginner echter een lastige stap om te maken.

In Svelte daarentegen, bestaat een component uit een script sectie, style sectie en html sectie. Het is hierdoor overzichtelijk en lijkt verder veel op de gewone JS, html en css. Het is voor de beginner dan ook vrij makkelijk te begrijpen. Echter, in de werkerlijkheid is de complexiteit als het ware gecamofleerd, om de programmeurs het makkelijk te maken.

## Svelte - Element Directives
Svelte heeft directives om elementen te manipuleren:


### Property variables

In Svelte kun je properties geven aan een component. Die properties zijn dan in de component beschikbaar door export en dan met dezelfde variabele naam declareren:

	// Parent component
	
	<script>
		import Navbar from "./nav/navbar.svelte";
		
		let name = "Nordin";
	</script>
	
	<main>
		<Navbar title={name}/>
	</main>
	
	
	
	// Child component
	
	<script>
		export let title;
	</script>
	
	<main>
	    <nav class="navbar is-light" role="navigation" aria-label="main navigation">
	        <div class="navbar-brand">
	            <a class="navbar-item">
	            <img src="https://bulma.io/images/bulma-logo.png" width="112" height="28" alt="logo">
	            {title}
	            </a>
	
	
		        <div id="navbarBasicExample" class="navbar-menu">
		            <div class="navbar-start">
		            <a class="navbar-item">
		                Home
		            </a>
		
		            </div>
		        </div>
	        </div>
	    </nav>
	</main>
	
 



### Classes toevoegen of verwijderen
In vanilla JS kun je een class toevoegen of verwijderen mbv classList, in Svelte kan dat eenvoudiger:

	<script>
		let show = false;
	
		setInterval(() => {
			show = !show;
			console.log(show);
		}, 2000);
	</script>
	
	<main>
		<div class:borderize={show}>My name is {name}</div>
	</main>
	
	<style>
		.borderize {
			border: 1px solid skyblue;
		}
	</style>

### Attributes toevoegen of verwijderen




### Svelte's addEventListener

Svelte heeft een eenvoudige manier van luisteren naar events. Zie voorbeeld hieronder voor het luisteren naar een click-event:

	<script>
		let count = 0;
	
		function handleClick(event) {
			count += 1;
		}
	</script>
	
	<button on:click={handleClick}>
		count: {count}
	</button>



### Reactive declaration

Klinkt complex, maar wil enkel zeggen dat variabelen in een stukje code, opnieuw uitgevoerd wordt als een variabele gewijzigd is. Het markeren van een reactive declaratie wordt gedaan met een **$:**-teken. Zie voorbeeld hieronder:

	<script>
		export let title;
		export let person;
	
		// this will update `document.title` whenever
		// the `title` prop changes
		$: document.title = title;
	
		$: {
			console.log(`multiple statements can be combined`);
			console.log(`the current title is ${title}`);
		}
	
		// this will update `name` when 'person' changes
		$: ({ name } = person);
	
		// don't do this. it will run before the previous line
		let name2 = name;
	</script>








