<script>
	//Imports
	import { onMount } from 'svelte';

	//Props
	export let allTopTracksLong;
	export let allTopTracksShort;

	//Variables
	let favDecadeLong;
	let favDecadeShort;

	//Functions
	const findTopDecade = (tracks) => {
		let allDates = tracks.map((track) => track.album.release_date.slice(0, 3));
		let dateCounts = {};

		allDates.forEach((date) => {
			if (dateCounts[date]) {
				dateCounts[date]++;
			} else {
				dateCounts[date] = 1;
			}
		});

		return `${Object.keys(dateCounts).reduce((a, b) => (dateCounts[a] > dateCounts[b] ? a : b))}0`;
	};

	onMount(() => {
		favDecadeLong = findTopDecade(allTopTracksLong);
		favDecadeShort = findTopDecade(allTopTracksShort);
	});
</script>

<h2 class="text-4xl text-center">Your Favorite Decade</h2>

<div class="my-3">
	<h3 class="my-2 text-center">CURRENT</h3>
	<div class="font-bold text-6xl py-3 text-center">{favDecadeShort}s</div>
</div>

<div class="my-3">
	<h3 class="my-2 text-center">ALL TIME</h3>
	<div class="font-bold text-6xl py-3 text-center">{favDecadeLong}s</div>
</div>

<!-- <p class="text-center">many of your all-time most played songs come from this decade</p> -->
