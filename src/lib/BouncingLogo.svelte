<script lang="ts">
	import { onMount, onDestroy } from 'svelte';
	import CatJumpGif from '$lib/assets/catjump.gif';
	export let parentWidth: number, parentHeight: number;
	export let fps = 30;
    export let startX = 0;
    export let startY = 0;

	let mover: NodeJS.Timeout | null = null;
	let logoContainer: HTMLDivElement;
	var logo: HTMLImageElement;
	let xDirection = 1;
	let yDirection = 1;
	let speed = 15;
	let currentX = startX || 0;
	let currentY = startY || 0;
	let currentWidth = 0;
	let currentHeight = 0;

	onMount(() => {
		mover = setInterval(() => {
			currentX += xDirection * speed;
			currentY += yDirection * speed;
			if (currentX > parentWidth - currentWidth || currentX < 0) {
				xDirection *= -1;
				logo.style.filter = `hue-rotate(${Math.floor(Math.random() * 360)}deg)`;
			}
			if (currentY > parentHeight - currentHeight || currentY < 0) {
				yDirection *= -1;
				logo.style.filter = `hue-rotate(${Math.floor(Math.random() * 360)}deg)`;
			}

			logoContainer.setAttribute('style', `position: absolute; top: ${currentY}px; left: ${currentX}px;`);
		}, 1000 / fps);
	});

	onDestroy(() => {
		if (mover) {
			clearInterval(mover);
		}
	});
</script>

<div
	class="container"
	bind:this={logoContainer}
	bind:clientWidth={currentWidth}
	bind:clientHeight={currentHeight}
>
	<img bind:this={logo} src={CatJumpGif} class="logo" alt="Cat jumping GIF" />
</div>

<style>
	.container {
		position: absolute;
		width: fit-content;
		height: fit-content;
		z-index: 100;
	}

	.logo {
		width: auto;
		height: 20vh;
        filter: hue-rotate(180deg);
        filter: saturate(8);
	}
</style>
