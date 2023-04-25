<script>
	//Imports
	import { onMount } from 'svelte';

	//Props
	export let allTopArtists;

	//Variables
	let allGenres = [];
	let genreCounts = [];
	let topFiveGenres

	//Functions
	onMount(() => {
		allTopArtists.forEach((artist) => {
			artist.genres.forEach((genre) => {
				allGenres.push(genre);
			});
		});

		allGenres.forEach((genre) => {
			if (genreCounts[genre]) {
				genreCounts[genre]++;
			} else {
				genreCounts[genre] = 1;
			}
		});

		let sortable = [];
		for (var genre in genreCounts) {
			sortable.push([genre, genreCounts[genre]]);
		}

		sortable.sort(function (a, b) {
			return b[1] - a[1];
		});

		topFiveGenres = sortable.slice(0, 5);

		console.log(topFiveGenres);
	});
</script>

<h2 class="text-4xl ">Your Top Genres</h2>
<div>
	{#if topFiveGenres}
		{#each topFiveGenres as genre, index}
			<p class="font-bold text-lg py-3">{index + 1}. {genre[0]}</p>
		{/each}
	{/if}
</div>
