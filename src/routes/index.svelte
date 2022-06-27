<script context="module" lang="ts">
	export const prerender = true;
</script>

<script lang="ts">
	import { ConfettiExplosion } from 'svelte-confetti-explosion';
	import Counter from '$lib/Counter.svelte';
	import { tick } from 'svelte';

	let done = false;

	let today = new Date();
	let anni = new Date(today.getFullYear(), 1, 13);
	let yearCount = today.getFullYear() - 2022;

	if (today.getMonth() > 1 || (today.getMonth() == 1 && today.getDate() > 13)) {
		anni.setFullYear(anni.getFullYear() + 1);
	}
	let remaining = (anni.getTime() - today.getTime()) / 1000;

	let x = 0;
	let y = 0;
	let isVisible = false;

	const handleClick = async (e: MouseEvent) => {
		x = e.clientX;
		y = e.clientY;

		isVisible = false;
		await tick();
		isVisible = true;
	};
</script>

<svelte:head>
	<title>Counting down to our anniversary</title>
	<meta name="description" content="Web app for counting down to our anniversary" />
</svelte:head>

<svelte:body on:click={handleClick} />

<section class={`container ${done ? 'done' : ''}`}>
	{#if done && isVisible}
		<ConfettiExplosion --x="{x}px" --y="{y}px" />
	{/if}
	<div class="inner">
		{#if !done}
			<Counter countdown={remaining} on:completed={() => (done = true)} />
		{/if}
		{#if done}
			<h1>{'I love you <3'}</h1>
			<p>Happy {yearCount} year{yearCount > 1 ? 's' : ''} anniversary!!</p>
			<p>🎉💕</p>
		{/if}
	</div>
</section>

<style>
	.container {
		height: 100%;
		width: 100%;
		background-color: white;
	}

	.container.done {
		background-color: #fcddea;
	}

	.inner {
		height: 100%;
		display: flex;
		flex-direction: column;
		justify-content: center;
		align-items: center;
		text-align: center;
	}

	h1 {
		font-size: 3.5rem;
		margin: 1rem;
		user-select: none;
	}

	p {
		font-size: 2rem;
		margin: 0;
		user-select: none;
	}
</style>
