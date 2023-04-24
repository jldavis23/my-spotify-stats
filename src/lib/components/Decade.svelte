<script>
	//Imports
	import { onMount } from 'svelte';

	//Props
	export let allTopTracks;

	let allDates = allTopTracks.map((track) => track.album.release_date.slice(0, 3));

    let dateCounts = {}

	allDates.forEach((date) => {
		if (dateCounts[date]) {
			dateCounts[date]++;
		} else {
			dateCounts[date] = 1;
		}
	});

	let favDecade = `${Object.keys(dateCounts).reduce((a, b) =>
		dateCounts[a] > dateCounts[b] ? a : b
	)}0`;

</script>

<div>Your Favorite Decade: {favDecade}</div>
