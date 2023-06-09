<script>
	//Imports
	import { onMount } from 'svelte';
	import SongModal from './SongModal.svelte';
	import Loader from './Loader.svelte';

	//Props
	export let accessToken;
	export let allTopTracksLong;
	export let allTopTracksShort;

	//Variables
	let topObscureTracks = [];
	let selectedTrack;

	//Functions
	onMount(async () => {
		await findObscureTracks();
	});

	const findObscureTracks = async () => {
		let everyTopTrack = [];
		allTopTracksLong.forEach((track) => {
			if (!allTopTracksShort.some((t) => t.id === track.id)) {
				everyTopTrack.push(track);
			}
		});

		everyTopTrack.sort((a, b) => {
			if (a.popularity < b.popularity) {
				return -1;
			}
			if (a.popularity > b.popularity) {
				return 1;
			}
			return 0;
		});

		let artistIds = everyTopTrack.map((track) => track.artists[0].id);
		let artists;

		try {
			const result = await fetch(`https://api.spotify.com/v1/artists?ids=${artistIds.join()}`, {
				method: 'GET',
				headers: { Authorization: `Bearer ${accessToken}` }
			});

			const artistData = await result.json();
			artists = artistData.artists;
		} catch (err) {
			console.log(err);
		}

		let filteredObscureTracks = [];
		everyTopTrack.forEach((track) => {
			const foundArtist = artists.find((artist) => artist.id === track.artists[0].id);
			if (foundArtist.popularity < 50) {
				filteredObscureTracks.push(track);
			}
		});

		topObscureTracks = filteredObscureTracks.slice(0, 6);
	};
</script>

<h2 class="text-4xl">Your Most Obscure Tracks</h2>

{#if topObscureTracks.length > 0}
	<div class="py-3 grid gap-6 md:grid-cols-2 lg:grid-cols-3">
		{#each topObscureTracks as track}
			<!-- svelte-ignore a11y-click-events-have-key-events -->
			<label
				class="flex items-center space-x-3 cursor-pointer"
				on:click={() => (selectedTrack = track)}
				for="my-modal-6"
			>
				<div class="avatar">
					<div class="w-12 h-12 bg-primary">
						{#if track.album.images[0]}
							<img src={track.album.images[0].url} alt={track.album.name} />
						{/if}
					</div>
				</div>

				<div class="text-left">
					<div class="font-bold">{track.name}</div>
					<div class="text-sm opacity-50">
						{#each track.artists as artist}
							<p>{artist.name} </p>
						{/each}
					</div>
				</div>
			</label>
		{/each}
	</div>
{:else}
	<Loader />
{/if}

{#if selectedTrack}
	<SongModal {selectedTrack} />
{/if}
