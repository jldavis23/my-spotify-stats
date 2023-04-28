<script>
	//Imports
	import { onMount } from 'svelte';

	//Props
	export let allTopArtistsShort;
	export let allTopArtistsLong;

	//Variables
	let topObscureArtists = [];

	//Functions
	const findObscureArtists = () => {
		let everyTopArtist = [];
		allTopArtistsLong.forEach((artist) => {
			if (!allTopArtistsShort.some((a) => a.id === artist.id)) {
				everyTopArtist.push(artist);
			}
		});

		everyTopArtist.sort((a, b) => {
			if (a.popularity < b.popularity) {
				return -1;
			}
			if (a.popularity > b.popularity) {
				return 1;
			}
			return 0;
		});

		topObscureArtists = everyTopArtist.slice(0, 6);
	};

    onMount(() => {
        findObscureArtists();
    })
</script>

<h2 class="text-4xl">Your Most Obscure Artists</h2>

<div class="py-3 grid gap-6 md:grid-cols-2 lg:grid-cols-3">
	{#each topObscureArtists as artist}
		<div class="tooltip tooltip-primary" data-tip="Album:  | Duration:">
			<div class="flex items-center space-x-3 cursor-default p-2">
				<div class="avatar">
					<div class="w-12 h-12 bg-primary">
						{#if artist.images[0]}
							<img src={artist.images[0].url} />
						{/if}
					</div>
				</div>

				<div class="text-left">
					<div class="font-bold">{artist.name}</div>
				</div>
			</div>
		</div>
	{/each}
</div>
