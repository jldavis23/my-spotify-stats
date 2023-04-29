<script>
	//Imports
	import { onMount } from 'svelte';
	import ArtistModal from './ArtistModal.svelte';
	import Loader from './Loader.svelte';

	//Props
	export let accessToken;
	export let userCountry;

	//Variables
	let artists = [];
	let btnTimeFrame;
	let selectedArtist;

	//Functions
	onMount(async () => {
		fetchTopArtists('long_term');
	});

	async function fetchTopArtists(timeFrame) {
		if (timeFrame === 'short_term') {
			btnTimeFrame = 'Last 4 Weeks';
		} else if (timeFrame === 'medium_term') {
			btnTimeFrame = 'Last 6 Weeks';
		} else if (timeFrame === 'long_term') {
			btnTimeFrame = 'All Time';
		}

		try {
			const result = await fetch(
				`https://api.spotify.com/v1/me/top/artists?time_range=${timeFrame}`,
				{
					method: 'GET',
					headers: { Authorization: `Bearer ${accessToken}` }
				}
			);

			const artistData = await result.json();
			artists = artistData.items;
			return artists;
		} catch (err) {
			console.log(err);
		}
	}

	// artists = [
	//     {name: "BTS"}
	// ]
</script>

<div class="lg:flex items-center gap-12">
	<h2 class="text-4xl">Your Top Artists</h2>
	<div class="btn-group my-5">
		<button
			class="btn btn-xs"
			class:btn-active={btnTimeFrame === 'All Time'}
			on:click={() => fetchTopArtists('long_term')}>All time</button
		>
		<button
			class="btn btn-xs"
			class:btn-active={btnTimeFrame === 'Last 6 Weeks'}
			on:click={() => fetchTopArtists('medium_term')}>Last 6 Weeks</button
		>
		<button
			class="btn btn-xs"
			class:btn-active={btnTimeFrame === 'Last 4 Weeks'}
			on:click={() => fetchTopArtists('short_term')}>Last 4 Weeks</button
		>
	</div>
</div>

{#if artists.length > 0}
	<div
		class="py-3 grid gap-3
md:grid-flow-col md:grid-cols-2 md:grid-rows-[repeat(10,_minmax(0,_1fr))]
lg:grid-cols-3 lg:grid-rows-[repeat(7,_minmax(0,_1fr))]"
	>
		{#each artists as artist, index}
			<!-- svelte-ignore a11y-click-events-have-key-events -->
			<label
				class="flex items-center space-x-3 cursor-pointer"
				on:click={() => (selectedArtist = artist)}
				for="my-modal-5"
			>
				<p class="w-1/12">{index + 1}.</p>

				<div class="avatar">
					<div class="w-12 h-12 b-primary">
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
