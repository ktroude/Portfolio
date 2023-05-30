<script lang="ts">
import { onMount } from 'svelte';

let invadersPack:Invader[] = [];

interface Invader{
		x:number,
		y:number,
		life:number,
		speed:number,
		status:number, // defining the strength of the invader | 0 = weak ; 1 = middle ; 2 = strong
	}
	
	

	function new_game() {

	}

	function create_invader(x:number, y:number, status:number) {
		let invader:Invader
		invader = {
			x:x,
			y:y,
			life: status + 1,
			speed:status + 1,
			status:status,
		}
		return invader;
	}

	// return between 10 et 20 weak invaders (will be assigned to invaderPack variale)
	function easy_game() {
		const min = 10;
		const max = 20;
		const randomNumber = Math.floor(Math.random() * (max - min + 1)) + min;
		
	}

	onMount( () => {
		for (let i=0; i < 1000; i += 100) {
			invadersPack.push(create_invader(100 + i, 500, 0));
		}
		console.log(invadersPack)
	}); 

</script>







<svelte:head>
	<title>Hello World!</title>
	<link rel="stylesheet" href="src/style.css">
</svelte:head>







<body class="body">

	{#each invadersPack as invader}
		{#if invader.status === 0}
			<div class="weak_invader" style="left: {invader.x}px; top: {invader.y}px;"></div>
		{/if}
		{#if invader.status === 1}
			<div class="middle_invader"></div>
		{/if}
		{#if invader.status === 2}
			<div class="strong_invader"></div>
		{/if}
	{/each}


</body>

