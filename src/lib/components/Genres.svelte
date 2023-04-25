<script>
	//Imports
	import { onMount } from 'svelte';

	//Props
	export let allTopArtistsLong;
	export let allTopArtistsShort;

	//Variables
	let allGenres = [];
	let genreCounts = [];
	let topFiveGenresLong;
	let topFiveGenresShort;

	//Functions
	const findTopGenres = (artists) => {
		artists.forEach((artist) => {
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

		return sortable.slice(0, 5);
	};

	onMount(() => {
		topFiveGenresLong = findTopGenres(allTopArtistsLong);
		topFiveGenresShort = findTopGenres(allTopArtistsShort);
	});
</script>

<h2 class="text-4xl text-center">Your Top Genres</h2>

{#if topFiveGenresLong && topFiveGenresShort}
	<div class="my-3">
		<h3 class="my-2 text-center">CURRENT</h3>
		<div>
			{#each topFiveGenresShort as genre, index}
				<p class="text-xl py-3">{index + 1}. <span class="font-bold">{genre[0]}</span></p>
			{/each}
		</div>
	</div>

	<div class="my-3">
		<h3 class="my-2 text-center">ALL TIME</h3>
		<div>
			{#each topFiveGenresLong as genre, index}
				<p class="text-xl py-3">{index + 1}. <span class="font-bold">{genre[0]}</span></p>
			{/each}
		</div>
	</div>
	
{/if}
