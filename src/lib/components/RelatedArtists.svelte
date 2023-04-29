<script>
	//Imports
	import { onMount } from 'svelte';
	import ArtistModal from './ArtistModal.svelte';
	import Loader from './Loader.svelte';

	//Props
	export let accessToken;
	export let allTopArtistsShort;
	export let userCountry;

	//Variables
	let allArtistRecs = [];
	let selectedArtist;

	//Functions
	onMount(async () => {
		await getRelatedArtists();
	});

	const getRelatedArtists = async () => {
		let random = Math.floor(Math.random() * (allTopArtistsShort.length-1));
		let artistId = allTopArtistsShort[random].id;

		try {
			const result = await fetch(`https://api.spotify.com/v1/artists/${artistId}/related-artists`, {
				method: 'GET',
				headers: { Authorization: `Bearer ${accessToken}` }
			});

			const data = await result.json();
			let artistRecs = data.artists;
			let filteredArtists = [];

			artistRecs.forEach((rec) => {
				if (!allTopArtistsShort.some((artist) => artist.id === rec.id)) {
					filteredArtists.push(rec);
				}
			});

			allArtistRecs = artistRecs;
			console.log(allArtistRecs);
		} catch (err) {
			console.log(err);
		}
	};
</script>

<h2 class="text-4xl">Artist Recommendations</h2>

{#if allArtistRecs.length > 0}
	<button class="btn my-6 btn-sm" on:click={getRelatedArtists}>Refresh</button>

	<div class="py-3 grid gap-6 md:grid-cols-2 lg:grid-cols-3">
		{#each allArtistRecs as artist}
			<!-- svelte-ignore a11y-click-events-have-key-events -->
			<label
				class="flex items-center space-x-3 cursor-pointer"
				on:click={() => (selectedArtist = artist)}
				for="my-modal-5"
			>
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
			</label>
		{/each}
	</div>
{:else}
	<Loader />
{/if}

{#if selectedArtist}
	<ArtistModal {selectedArtist} {accessToken} {userCountry} />
{/if}
