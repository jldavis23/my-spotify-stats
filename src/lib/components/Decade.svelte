<script>
	//Imports
	import { onMount } from 'svelte';

	//Props
	export let allTopTracks;

	//Variables
	let favDecade

	//Functions
	onMount(() => {
		let allDates = allTopTracks.map((track) => track.album.release_date.slice(0, 3));
		let dateCounts = {};

		allDates.forEach((date) => {
			if (dateCounts[date]) {
				dateCounts[date]++;
			} else {
				dateCounts[date] = 1;
			}
		});

		favDecade = `${Object.keys(dateCounts).reduce((a, b) =>
			dateCounts[a] > dateCounts[b] ? a : b
		)}0`;
	});
</script>

<h2 class="text-4xl ">Your Favorite Decade</h2>
<div class="font-bold text-lg py-3">{favDecade}s</div>
