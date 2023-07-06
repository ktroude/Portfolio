<script lang="ts">
	import { writable } from 'svelte/store';
	import { onMount } from 'svelte';
	import { TweenMax } from 'gsap';

  
	let loading: boolean = false;
	let invadersPack: Invader[] = [];
	const invadersPackStore = writable(invadersPack);
 
	let startCode:boolean = false;
	let userInputNb:number = 0;
	let userCode:string = '';
	let code:string = '';

	let fontSize:number = 3;
  	let width:number = 80;
	let reduce:boolean = false;

	let startScroll:boolean = false;

	let isLargeScreen:boolean = false;


	interface Invader {
	  x: number;
	  y: number;
	  life: number;
	  speed: number;
	  status: number;
	  dir: string;
	  timer: number;
	}
  
	function getDir(div: Invader): Invader {
	  const random = Math.random();
	  if (random < 0.25) {
		div.dir = "right";
	  } else if (random < 0.5) {
		div.dir = "left";
	  } else if (random < 0.75) {
		div.dir = "up";
	  } else {
		div.dir = "down";
	  }
	  return div;
	}
  
	function move(div: Invader): Invader {
	  if (div.dir == "right") {
		div.x += div.speed;
		if (div.x > window.innerWidth)
			div.x = 0;	
	  }
	  if (div.dir == "left") {
		div.x -= div.speed;
		if (div.x < 0)
			div.x = window.innerWidth; 
	  }
	  if (div.dir == "up") {
		div.y += div.speed;
		if (div.y > window.innerHeight)
			div.y = 0;
	  }
	  if (div.dir == "down") {
		div.y -= div.speed;
		if (div.y < 0)
			div.y = window.innerHeight;
	  }
	  return div;
	}

	function createInvaders(nb: number) {
	//  for (let i = 0; i < nb; i++) {
		invadersPack = [...invadersPack, create_invader(35.5, 57, 0)];
	//	getDir(invadersPack[i]);
	//  }
	//	invadersPackStore.set(invadersPack);
	}

	function create_invader(x: number, y: number, status: number): Invader {
		const randomDuration = Math.floor(Math.random() * (10000 - 1000 + 1)) + 1000;
	  return {
		x,
		y,
		life: status + 1,
		speed: status + 2,
		status: status,
		dir: "",
		timer: randomDuration,
	  };
	}
  


// 	function executeInvader(invader: Invader): Promise<void> {
//   return new Promise((resolve) => {
//     const randomDuration = Math.floor(Math.random() * (5000 - 2000 + 1)) + 2000;
//     invader = getDir(invader);
//     invader = move(invader);
//     invader.timer = randomDuration;
//     invadersPack = invadersPack.map((item) => (item === invader ? invader : item)); // Mettre à jour les coordonnées dans le tableau
//     invadersPackStore.set(invadersPack); // Mettre à jour le store
//     if (invader.timer > 0) {
//       setTimeout(() => {
//         executeInvader(invader).then(resolve);
//       }, invader.timer);
//     } else {
//       resolve();
//     }
//   });
// }

function sleep(ms: number): Promise<void> {
  return new Promise(resolve => setTimeout(resolve, ms));
}

function startInvaders() {
	const intervalDuration = 16; // Durée en millisecondes entre chaque rafraîchissement
 	setInterval(() => {
    invadersPack = invadersPack.map((invader) => {
      invader = move(invader);
      invader.timer -= intervalDuration; // --> Utiliser ft_usleep() plutôt ? Plus de précision, à voir donc

      // Vérifier si le timer a expiré
      if (invader.timer <= 0) {
        invader = getDir(invader);
        invader.timer = Math.floor(Math.random() * (10000 - 1000 + 1)) + 1000;
      }

      return invader;
    });
    invadersPackStore.set(invadersPack);
}, intervalDuration);
}


function initCode() {
	startCode = true;
	window.addEventListener('keydown', stopSpaceKey)
	window.addEventListener('keypress', handleKeyPress);
	window.addEventListener('keypress', moveCodeDiv);
	code = '	function create_invader(x: number, y: number, status: number): Invader {\
		const randomDuration = Math.floor(Math.random() * (10000 - 1000 + 1)) + 1000;\
	  return {\
		x,\
		y,\
		life: status + 1,\
		speed: status + 2,\
		status: status,\
		dir: "" \
		timer: randomDuration,\
	  };\
	}'
}

async function moveCodeDiv() {
	if (userInputNb >= code.length / 4) {
		stopCode();
		await sleep(300);
		while (true) {
			// if (width > 20)
				width -= 2;
			// else
			// 	width = 10;
			if (fontSize > 0)
				fontSize -= 0.1;
			await sleep(20);
			if (fontSize == 0)
				break;
		}
	}
}

function handleKeyPress(event: KeyboardEvent) {
	userInputNb++;
	displayCode();
}

function stopCode() {
//	startCode = false;
	userInputNb = 0;	
	stopCaretInterval();
	window.removeEventListener('keypress', handleKeyPress);
}

function displayCode() {
	userCode = code.slice(0, userInputNb * 4);
}

let caretPosition:number = 0;
let caretVisible:boolean = false;
let caretInterval:any;

function startCaretInterval() {
    caretInterval = setInterval(() => {
      caretVisible = !caretVisible;
    }, 500);
}

function stopCaretInterval() {
	clearInterval(caretInterval);
}

  function handleScroll() {
    if (window.innerHeight >= document.body.offsetHeight && startScroll == false) {

    }
  }

  function stopSpaceKey(event:KeyboardEvent) {
	if (event.key === ' ') {
    	event.preventDefault();
		userInputNb++;
		displayCode();
  	}
  }

	function handleResize() {
    	const mediaQuery = window.matchMedia('(min-width: 1155px)');
    	isLargeScreen = mediaQuery.matches;
  	}
 
  onMount(() => {
	    handleResize();
		window.addEventListener('resize', handleResize);
		startCaretInterval();
		createInvaders(10);
	  //  startInvaders();
		loading = true;
	});
	</script>
  
  
  <svelte:head>
	  <title>Hello World!</title>
	  <link rel="stylesheet" href="css/style.css">
	</svelte:head>
	

	
	<body class="body">
		{#if loading === true}
		
		<!-- {#each invadersPack as invader}
		<div class='invader' style="left: calc({invader.x}vw); top: calc({invader.y}vh);"></div>
		{/each} -->
		
		<div class="header_box">
			<p class="header_text">Hello World! Do you need a developer?</p> 
			<img class="header_invader" src='css/img/header/Groupe 2.svg' alt="big invader">
		</div>
		
		<div class='presentation_box'>
			<p class='presentation_text' style='margin-top: 91px;'>	My name is Kevin Troude, and I’m a developer.
				<p class='presentation_text'>	I studied in school 42. I can write in C, C++,<br> Javascript, Python, and I can learn quickly any further language.
					<p class='presentation_text' style='margin-bottom: 76px;'>	I bet we can make a great work together,<br>wanna give it a try ?
					</div>
					
					<div class='code_box'>
		<div class="code_img_container">
			{#if isLargeScreen}
				<img class='code_img' src='css/img/code_box/Rectangle 310.svg' alt='black rectangle'>
			{:else}
				<img class='code_img' src='css/img/code_box/Groupe 18.svg' alt='black rectangle'>
			{/if}
				
			{#if startCode === false}
				<p class="code_text">Let’s work together.<br>Write anything <button class="code_button" on:click={() => initCode()}> in here {#if caretVisible == true }|{/if} </button></p>
				{:else if fontSize > 0}
				<p class="code" style="	font: normal normal normal {fontSize}vw/{fontSize}vw Minimo;
				width: {width}%">
					{userCode} {#if caretVisible == true }|{/if} 
			{/if}
		</div>
	</div>

	<div class="project_box_container">
		<div class="project_box">
			<div class="project_1">
				<button class="project_button"></button>
				<img class="project_img" src="css/img/project/Groupe 16.svg" alt="">
				<p class="project_text">Développement<br>front-end et back-end</p>
			</div>
			<div class='space'></div>
			<div class="project_2">
				<button class="project_button"></button>
				<img class="project_img" src="css/img/project/Groupe 14.svg" alt="">
				<p class="project_text">Création<br>de site web</p>
			</div>
			<div class='space'></div>
			<div class="project_3">
				<button class="project_button"></button>
				<img class="project_img" src="css/img/project/Groupe 12.svg" alt="">
				<p class="project_text">Développement de logiciel (utilisation d’algorithmes)</p>
			</div>
		</div>
	</div>
		
		<div class="work_box">
			<p class="work_text">Would you like to work with me ?</p>
		</div>
		
	{/if}

</body>
