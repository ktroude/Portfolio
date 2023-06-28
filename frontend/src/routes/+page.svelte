<script lang="ts">
	import { writable } from 'svelte/store';
	import { onMount } from 'svelte';
  
	let loading: boolean = false;
	let invadersPack: Invader[] = [];
	const invadersPackStore = writable(invadersPack);
  
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
	  for (let i = 0; i < nb; i++) {
		invadersPack = [...invadersPack, create_invader(i * 100, i*100, 0)];
		getDir(invadersPack[i]);
	  }
	  invadersPackStore.set(invadersPack);
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



  
	onMount(() => {
	  createInvaders(10);
	  startInvaders();
	  loading = true;
	});
  </script>
  

<svelte:head>
	<title>Hello World!</title>
	<link rel="stylesheet" href="css/style.css">
</svelte:head>

<body class="body">
	{#if loading === true}

<div class="header_div">
	<p class="header_text">Hello world, do you need a developer ?</p>	
	<div class="header_rectangle"> </div>
	<div class="header_invader"> </div>
</div>


<div class="main_text_container"> 
<p class="main_text"> My name is Kevin Troude, and I’m a developer. </p>
<p class="main_text"> I studied in school 42. I can write in C, C++, Javascript, </p>
<p class="main_text"> Python, and I can learn quickly any further language. </p>
<p class="main_text"> I bet we can make a great work together, </p>
<p class="main_text"> wanna give it a try ? </p>
</div>



		{#each invadersPack as invader}
		<div class= "weak_invader" style="left: {invader.x}px; top: {invader.y}px;"> </div>
		{/each}
	{/if}

</body>
