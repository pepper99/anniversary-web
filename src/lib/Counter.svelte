<script lang="ts">
	import { createEventDispatcher } from 'svelte';
	import { onMount, onDestroy, afterUpdate } from 'svelte';
	import { tweened } from 'svelte/motion';
	export let countdown = 0;
	const dispatch = createEventDispatcher();

	const digitHeight = 105;

	let timer: NodeJS.Timeout | null = null;
	const numbers = [0, 1, 2, 3, 4, 5, 6, 7, 8, 9].reverse();
	$: minutes = Math.floor((countdown / 60) % 60);
	$: hours = Math.floor((countdown / 60 / 60) % 24);
	$: days = Math.floor(countdown / 60 / 60 / 24);
	$: seconds = countdown % 60;

	let secondH = tweened((9 - Math.floor(seconds / 10)) * digitHeight, { duration: 300 });
	let secondL = tweened((9 - Math.floor(seconds % 10)) * digitHeight, { duration: 300 });

	let minuteH = tweened((9 - Math.floor(minutes / 10)) * digitHeight, { duration: 300 });
	let minuteL = tweened((9 - Math.floor(minutes % 10)) * digitHeight, { duration: 300 });

	let hourH = tweened((9 - Math.floor(hours / 10)) * digitHeight, { duration: 300 });
	let hourL = tweened((9 - Math.floor(hours % 10)) * digitHeight, { duration: 300 });

	let dayFirst = tweened((9 - Math.floor(days / 100)) * digitHeight, { duration: 300 });
	let daySecond = tweened((9 - Math.floor(days / 10)) * digitHeight, { duration: 300 });
	let dayThird = tweened((9 - Math.floor(days % 10)) * digitHeight, { duration: 300 });

	onMount(() => {
		timer = setInterval(() => {
			countdown -= 1;
		}, 1000);
	});

	afterUpdate(() => {
		dayFirst.set((9 - Math.floor(days / 100)) * digitHeight);
		daySecond.set((9 - Math.floor((days / 10) % 10)) * digitHeight);
		dayThird.set((9 - Math.floor(days % 10)) * digitHeight);

		hourH.set((9 - Math.floor(hours / 10)) * digitHeight);
		hourL.set((9 - Math.floor(hours % 10)) * digitHeight);

		minuteH.set((9 - Math.floor(minutes / 10)) * digitHeight);
		minuteL.set((9 - Math.floor(minutes % 10)) * digitHeight);

		secondH.set((9 - Math.floor(seconds / 10)) * digitHeight);
		secondL.set((9 - Math.floor(seconds % 10)) * digitHeight);
	});

	onDestroy(() => {
		if (timer) {
			clearInterval(timer);
		}
	});

	$: {
		if (countdown <= 0) {
			clearInterval(timer);
			timer = null;
			dispatch('completed');
		}
	}
</script>

<div class="container">
	<div class="digits-container">
		<ul>
			{#each numbers as num, _}
				<li class="num" style="transform: translateY(-{$dayFirst}px);">
					<span>{num}</span>
				</li>
			{/each}
		</ul>
		<ul>
			{#each numbers as num, _}
				<li class="num" style="transform: translateY(-{$daySecond}px);">
					<span>{num}</span>
				</li>
			{/each}
		</ul>
		<ul>
			{#each numbers as num, _}
				<li class="num" style="transform: translateY(-{$dayThird}px);">
					<span>{num}</span>
				</li>
			{/each}
		</ul>
	</div>

	<p class="digit-seperator">:</p>

	<div class="digits-container">
		<ul>
			{#each numbers as num, _}
				<li class="num" style="transform: translateY(-{$hourH}px);">
					<span>{num}</span>
				</li>
			{/each}
		</ul>
		<ul>
			{#each numbers as num, _}
				<li class="num" style="transform: translateY(-{$hourL}px);">
					<span>{num}</span>
				</li>
			{/each}
		</ul>
	</div>

	<p class="digit-seperator">:</p>

	<div class="digits-container">
		<ul>
			{#each numbers as num, _}
				<li class="num" style="transform: translateY(-{$minuteH}px);">
					<span>{num}</span>
				</li>
			{/each}
		</ul>
		<ul>
			{#each numbers as num, _}
				<li class="num" style="transform: translateY(-{$minuteL}px);">
					<span>{num}</span>
				</li>
			{/each}
		</ul>
	</div>

	<p class="digit-seperator">:</p>

	<div class="digits-container">
		<ul>
			{#each numbers as num, _}
				<li class="num" style="transform: translateY(-{$secondH}px);">
					<span>{num}</span>
				</li>
			{/each}
		</ul>
		<ul>
			{#each numbers as num, _}
				<li class="num" style="transform: translateY(-{$secondL}px);">
					<span>{num}</span>
				</li>
			{/each}
		</ul>
	</div>
</div>

<style>
	.digits-container {
		display: flex;
	}

	ul {
		display: inline-block;
		list-style: none;
		padding-left: 0;
		height: 94px;
		overflow: hidden;
	}

	.num {
		font-size: 94px;
		user-select: none;
	}

	.container {
		display: flex;
		justify-content: center;
		align-items: center;
	}

	@media (max-width: 480px) {
		.container {
			flex-direction: column;
		}

		.digit-seperator {
			display: none;
		}
	}

	p {
		margin: 0 0;
		font-size: 94px;
		user-select: none;
		line-height: 1em;
	}
	
	span {
		font-size: 105px;
		line-height: 1em;
	}
	
	li {
		height: 105px;
		margin: 0 0;
	}
</style>
