<script>
	//Imports
	import { onMount } from 'svelte';
	import SongModal from './SongModal.svelte';
	import Loader from './Loader.svelte';

	//Props
	export let accessToken;
	export let allTopTracksShort;
	export let userId;

	//Variables
	let allTrackRecs = [];
	let selectedTrack;
	let toast;

	//Functions
	onMount(async () => {
		await findTrackRecs();
	});

	const findTrackRecs = async () => {
		let random = Math.floor(Math.random() * 46);
		let randomTracks = allTopTracksShort.slice(random, random + 5);
		let trackIds = randomTracks.map((track) => track.id);

		try {
			const result = await fetch(
				`https://api.spotify.com/v1/recommendations?limit=30&seed_tracks=${trackIds.join()}`,
				{
					method: 'GET',
					headers: { Authorization: `Bearer ${accessToken}` }
				}
			);

			const data = await result.json();
			const recs = data.tracks;
			let trackRecs = [];

			recs.forEach((rec) => {
				if (!allTopTracksShort.some((track) => track.id === rec.id)) {
					trackRecs.push(rec);
				}
			});

			allTrackRecs = trackRecs;
		} catch (err) {
			console.log(err);
		}
	};

	async function createPlaylist() {
		try {
			const result = await fetch(`https://api.spotify.com/v1/users/${userId}/playlists`, {
				method: 'POST',
				headers: { Authorization: `Bearer ${accessToken}` },
				body: JSON.stringify({
					name: `Reccomendations - My Spotify Stats`
				})
			});
			const data = await result.json();
			addSongsToPlaylist(data.id);
		} catch (err) {
			console.log(err);
		}
	}

	const addSongsToPlaylist = async (playlistId) => {
		let trackUris = allTrackRecs.map((track) => track.uri);

		try {
			const result = await fetch(`https://api.spotify.com/v1/playlists/${playlistId}/tracks`, {
				method: 'POST',
				headers: { Authorization: `Bearer ${accessToken}` },
				body: JSON.stringify({ uris: trackUris })
			});
			toast = true;
		} catch (err) {
			console.log(err);
		}
	};
</script>

<h2 class="text-4xl">Track Recommendations</h2>

{#if allTrackRecs.length > 0}
	<button class="btn my-6 btn-sm" on:click={findTrackRecs}>Refresh</button>

	<div class="py-3 grid gap-6 md:grid-cols-2 lg:grid-cols-3">
		{#each allTrackRecs as track}
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

	{#if toast}
		<div class="toast toast-end">
			<div class="alert alert-success">
				<div>
					<span>Playlist created!</span>
					<button class="btn btn-circle btn-outline btn-xs" on:click={() => toast = false}>
						<svg
							xmlns="http://www.w3.org/2000/svg"
							class="h-4 w-4"
							fill="none"
							viewBox="0 0 24 24"
							stroke="currentColor"
							><path
								stroke-linecap="round"
								stroke-linejoin="round"
								stroke-width="2"
								d="M6 18L18 6M6 6l12 12"
							/></svg
						>
					</button>
				</div>
			</div>
		</div>
	{/if}
{:else}
	<Loader />
{/if}

{#if selectedTrack}
	<SongModal {selectedTrack} />
{/if}

<button class="btn my-6" on:click={() => createPlaylist()}>+ Create Playlist</button>
