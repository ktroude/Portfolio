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
		invadersPack = [...invadersPack, create_invader(i + 20, i + 20, 0)];
	  }
	  invadersPackStore.set(invadersPack);
	}
  
	function create_invader(x: number, y: number, status: number): Invader {
	  return {
		x,
		y,
		life: status + 1,
		speed: status + 2,
		status: status,
		dir: "",
		timer: 0,
	  };
	}
  
	function executeInvader(invader: Invader): Promise<void> {
  return new Promise((resolve) => {
    const randomDuration = Math.floor(Math.random() * (5000 - 2000 + 1)) + 2000;
    invader = getDir(invader);
    invader = move(invader);
    invader.timer = randomDuration;
    invadersPack = invadersPack.map((item) => (item === invader ? invader : item)); // Mettre à jour les coordonnées dans le tableau
    invadersPackStore.set(invadersPack); // Mettre à jour le store
    if (invader.timer > 0) {
      setTimeout(() => {
        executeInvader(invader).then(resolve);
      }, invader.timer);
    } else {
      resolve();
    }
  });
}



function startInvaders() {
  const intervalDuration = 16; // Duree en millisecondes entre chaque rafraichissement
  const randomDuration = Math.floor(Math.random() * (10000 - 1000 + 1)) + 1000;
  const intervalId = setInterval(() => {
    invadersPack = invadersPack.map((invader) => {
      invader = move(invader);
      invader.timer -= intervalDuration; // --> Utiliser ft_usleep() plutot? Plus de précision, a voir donc

      // Vérifier si le timer a expiré
      if (invader.timer <= 0) {
        invader = getDir(invader);
        invader.timer = randomDuration;
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

<div class="header">
	<p class="header_text">Hello world, do you need a developer ?</p>	
	<img class="header_rectangle" src="img/Rectangle 1.png" alt="beautifull shaders">
	<img class="header_invader" src="img/Groupe 2/Groupe 2.png" alt="big space invader">
</div>

<div class="2nd_screen">

</div>


	{#if loading === true}
		{#each invadersPack as invader}
			{#if invader.status === 0}
				<div class="weak_invader" style="left: {invader.x}px; top: {invader.y}px;"></div>
			{/if}
			{#if invader.status === 1}
				<div class="middle_invader" style="left: {invader.x}px; top: {invader.y}px;"></div>
			{/if}
			{#if invader.status === 2}
				<div class="strong_invader" style="left: {invader.x}px; top: {invader.y}px;"></div>
			{/if}
		{/each}
	{/if}
</body>
