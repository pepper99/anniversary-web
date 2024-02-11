<script context="module" lang="ts">
	export const prerender = true;
</script>

<script lang="ts">
	import { ConfettiExplosion } from 'svelte-confetti-explosion';
	import Counter from '$lib/Counter.svelte';
	import BouncingLogo from '$lib/BouncingLogo.svelte';
	import { tick } from 'svelte';
	import YouTube from 'svelte-youtube';
	import { writable } from 'svelte/store';

	let today = new Date();
	let anni = new Date(today.getFullYear(), 1, 12);
	let yearCount = today.getFullYear() - 2021;
	let done = today.getDate() === anni.getDate() && today.getMonth() === anni.getMonth();

	let innerContainerWidth = 0;
	let innerContainerHeight = 0;

	if (anni < today) {
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

	let catStores = writable([
		{
			parentWidth: innerContainerWidth,
			parentHeight: innerContainerHeight,
			startX: Number(x),
			startY: Number(y)
		}
	]);

	const handleClick = async (e: MouseEvent) => {
		x = `${e.clientX}px`;
		y = `${e.clientY}px`;

		isVisible = false;
		await tick();
		isVisible = true;

		if ($catStores.length < 5) {
			$catStores[$catStores.length] = {
				parentWidth: innerContainerWidth,
				parentHeight: innerContainerHeight,
				startX: e.clientX,
				startY: e.clientY
			};
		}
	};

	let youtubeOptions = {
		height: '80%',
		width: '80%',
		playerVars: {
			mute: 0,
			autoplay: 1,
			enablejsapi: 1,
			iv_load_policy: 3,
			loop: 1,
			playsinline: 1,
			rel: 0,
			showinfo: 0,
			start: 0
		}
	};
</script>

<svelte:head>
	<title>Counting down to our anniversary</title>
	<meta name="description" content="Web app for counting down to our anniversary" />
</svelte:head>

<svelte:body on:click={handleClick} />

<section class={`container ${done ? 'done' : ''}`}>
	<div
		class="inner"
		bind:clientWidth={innerContainerWidth}
		bind:clientHeight={innerContainerHeight}
	>
		{#if done}
			<div class="yt-container">
				<YouTube videoId="wh9QLjk3M2k" options={youtubeOptions} />
			</div>
			<div class="confetti-container">
				{#if isVisible}
					<ConfettiExplosion --x={x} --y={y} />
				{/if}
				{#if initialVisible}
					<ConfettiExplosion --x="50vw" --y="50vh" />
				{/if}
			</div>
			<div class="bg" />
			<h1>{'I love you <3'}</h1>
			<p>Happy {yearCount}{getOrdinal(yearCount)} anniversary!!</p>
			<p>🎉😽😻🐈💕</p>

			{#each $catStores as item}
				<svelte:component
					this={BouncingLogo}
					parentWidth={innerContainerWidth}
					parentHeight={innerContainerHeight}
					startX={item.startX}
					startY={item.startY}
				/>
			{/each}
		{/if}
		{#if !done}
			<Counter countdown={remaining} on:completed={() => (done = true)} />
		{/if}
	</div>
</section>

<style>
	.container {
		height: 100%;
		width: 100%;
		background-color: white;
	}

	.yt-container {
		z-index: 50;
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
