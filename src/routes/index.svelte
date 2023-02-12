<script context="module" lang="ts">
	export const prerender = true;
</script>

<script lang="ts">
	import { ConfettiExplosion } from 'svelte-confetti-explosion';
	import Counter from '$lib/Counter.svelte';
	import { tick } from 'svelte';

	let today = new Date();
	let anni = new Date(today.getFullYear(), 1, 13);
	let yearCount = today.getFullYear() - 2021;
	let done = today.getDate() === anni.getDate() && today.getMonth() === anni.getMonth();

	if (today.getMonth() > 1 || (today.getMonth() == 1 && today.getDate() > 13)) {
		anni.setFullYear(anni.getFullYear() + 1);
	}
	let remaining = (anni.getTime() - today.getTime()) / 1000;

	let x = '50%';
	let y = '50%';
	let isVisible = false;
	let initialVisible = done;

	function getOrdinal(n: number) {
		let ord = 'th';

		if (n % 10 == 1 && n % 100 != 11) {
			ord = 'st';
		} else if (n % 10 == 2 && n % 100 != 12) {
			ord = 'nd';
		} else if (n % 10 == 3 && n % 100 != 13) {
			ord = 'rd';
		}

		return ord;
	}

	const handleClick = async (e: MouseEvent) => {
		x = `${e.clientX}px`;
		y = `${e.clientY}px`;
		console.log(x, y);

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
	<div class="inner">
		{#if done}
			<div class="confetti-container">
				{#if isVisible}
					<ConfettiExplosion --x={x} --y={y} />
				{/if}
				{#if initialVisible}
					<ConfettiExplosion --x="50vw" --y="50vh" />
				{/if}
			</div>
		{/if}
		{#if !done}
			<Counter countdown={remaining} on:completed={() => (done = true)} />
		{/if}
		{#if done}
			<div class="bg" />
			<h1>{'I love you <3'}</h1>
			<p>Happy {yearCount}{getOrdinal(yearCount)} anniversary!!</p>
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

	.confetti-container {
		position: absolute;
		height: 100vh;
		width: 100vw;
	}

	.bg {
		position: absolute;
		z-index: -1;
		background-image: url('$lib/assets/mumu.jpg');
		filter: blur(4px);
		-webkit-filter: blur(4px);
		height: 100%;
		width: 100%;
		background-position: center;
		background-repeat: no-repeat;
		background-size: cover;
		background-color: #fcddea;
	}

	.container.done {
		background-color: transparent;
	}

	h1 {
		--bg-size: 400%;
		--color-one: rgb(248, 146, 188);
		--color-two: rgb(219, 14, 59);
		font-size: clamp(3rem, 25vmin, 8rem);
		background: linear-gradient(90deg, var(--color-one), var(--color-two), var(--color-one)) 0 0 /
			var(--bg-size) 100%;
		color: transparent;
		-webkit-background-clip: text;
		background-clip: text;
		animation: move-bg 8s infinite linear;
	}
	@keyframes move-bg {
		to {
			background-position: var(--bg-size) 0;
		}
	}

	p {
		color: white;
	}

	.inner {
		height: 100%;
		display: flex;
		flex-direction: column;
		justify-content: center;
		align-items: center;
		text-align: center;
	}

	.pic {
		margin: 8px 32px;
		height: 300px;
		width: auto;
		border-radius: 16px;
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
