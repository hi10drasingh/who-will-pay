<script lang="ts">
	import { onMount } from 'svelte';
	import { tweened } from 'svelte/motion';
	import { cubicOut } from 'svelte/easing';

	const spinFactor = tweened(0, {
		duration: 3000,
		easing: cubicOut
	});

	type Player = { name: string; id: number };

	let masterList: Player[] = [
		{ name: 'hi10dra', id: 1 },
		{ name: 'patel', id: 2 },
		{ name: 'chamtesh', id: 3 },
		{ name: 'chinay sir', id: 4 },
		{ name: 'chatidar', id: 5 },
		{ name: 'chultamus', id: 6 },
		{ name: 'ady', id: 7 }
	];

	const shuffleArray = (arr: Player[]): Player[] => {
		const n = arr.length;
		for (let i = n - 1; i > 0; i--) {
			// Generate random index j such that 0 <= j <= i
			const j = Math.floor(Math.random() * (i + 1));
			// Swap elements arr[i] and arr[j]
			[arr[i], arr[j]] = [arr[j], arr[i]];
		}

		return arr;
	};

	let audio: HTMLAudioElement;
	onMount(() => {
		audio = new Audio('/Tick.mp3');
		audio.playbackRate = 0;
		audio.loop = true;
		audio.play();
		let fadeout = setInterval(function () {
			if (audio.playbackRate > 0) {
				audio.playbackRate -= 0.5;
			}
		}, 800);

		return () => clearInterval(fadeout);
	});

	$: if ($spinFactor === 1) {
		if (audio) audio.playbackRate = 0;
		spinning = false;
		spinFactor.set(0, {
			duration: 0
		});
	}

	let spinning = false;
	const handleClick = () => {
		if (!spinning) {
			spinFactor.set(1);
			spinning = true;
			masterList = shuffleArray(masterList);
			audio.playbackRate = 2;
		}
	};
</script>

<div class="wheel" style="--_items:{masterList.length};">
	<div class="wheel-section" style="--_spinFactor: {$spinFactor}">
		{#each masterList as { name, id }, index}
			<div class="wheel-section-item" style="--_idx: {index + 1}; --_initailIdx: {id + 1};">
				{name}
			</div>
		{/each}
	</div>
	<button type="button" on:click={handleClick} disabled={spinning}>SPIN</button>
</div>

<style>
	:where(.wheel) {
		all: unset;
		aspect-ratio: 1 / 1;
		container-type: inline-size;
		direction: ltr;
		display: grid;
		position: relative;
		width: 100%;
		&::after {
			aspect-ratio: 1 / cos(30deg);
			background-color: crimson;
			clip-path: polygon(50% 100%, 100% 0, 0 0);
			content: '';
			height: 4cqi;
			position: absolute;
			place-self: start center;
			scale: 1.4;
		}

		& > * {
			position: absolute;
		}

		button {
			aspect-ratio: 1 / 1;
			background: hsla(0, 0%, 100%, 0.8);
			border: 0;
			border-radius: 50%;
			cursor: pointer;
			font-size: 5cqi;
			place-self: center;
			width: 20cqi;
		}

		.wheel-section {
			all: unset;
			clip-path: inset(0 0 0 0 round 50%);
			display: grid;
			inset: 0;
			transform-origin: center;
			transform: rotate(calc(1440deg * var(--_spinFactor)));
			place-content: center start;

			.wheel-section-item {
				align-content: center;
				aspect-ratio: 1 / calc(2 * tan(180deg / var(--_items)));
				background: hsl(calc(360deg / var(--_items) * calc(var(--_initailIdx))), 100%, 75%);
				clip-path: polygon(0% 0%, 100% 50%, 0% 100%);
				display: grid;
				font-size: 5cqi;
				grid-area: 1 / -1;
				padding-left: 1ch;
				rotate: calc(360deg / var(--_items) * calc(var(--_idx) - 1));
				transform-origin: center right;
				user-select: none;
				width: 50cqi;
				font-weight: bold;
				text-transform: uppercase;
			}
		}
	}
</style>
