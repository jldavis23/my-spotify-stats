<script>
	//Imports
	import { onMount } from 'svelte';
	import SongModal from './SongModal.svelte';

	//Props
	export let accessToken;
	export let userId;

	//Variables
	let tracks = [];
	let trackUris = [];
	let btnTimeFrame;
	let selectedTrack;

	//Functions
	onMount(async () => {
		tracks = await fetchTopTracks('long_term');
	});

	async function fetchTopTracks(timeFrame) {
		if (timeFrame === 'short_term') {
			btnTimeFrame = 'Last 4 Weeks';
		} else if (timeFrame === 'medium_term') {
			btnTimeFrame = 'Last 6 Weeks';
		} else if (timeFrame === 'long_term') {
			btnTimeFrame = 'All Time';
		}

		try {
			const result = await fetch(
				`https://api.spotify.com/v1/me/top/tracks?time_range=${timeFrame}`,
				{
					method: 'GET',
					headers: { Authorization: `Bearer ${accessToken}` }
				}
			);

			const trackData = await result.json();
			tracks = trackData.items;
			trackUris = tracks.map((track) => track.uri);
			return tracks;
		} catch (err) {
			console.log(err);
		}
	}

	async function createPlaylist(time) {
		try {
			const result = await fetch(`https://api.spotify.com/v1/users/${userId}/playlists`, {
				method: 'POST',
				headers: { Authorization: `Bearer ${accessToken}` },
				body: JSON.stringify({
					name: `Top Tracks - ${time}`
				})
			});
			const data = await result.json();
			addSongsToPlaylist(data.id);
		} catch (err) {
			console.log(err);
		}
	}

	const addSongsToPlaylist = async (playlistId) => {
		try {
			const result = await fetch(`https://api.spotify.com/v1/playlists/${playlistId}/tracks`, {
				method: 'POST',
				headers: { Authorization: `Bearer ${accessToken}` },
				body: JSON.stringify({ uris: trackUris })
			});
			console.log('playlist created!');
		} catch (err) {
			console.log(err);
		}
	};
</script>

<div class="lg:flex items-center gap-12 ">
	<h2 class="text-4xl">Your Top Tracks</h2>
	<div class="btn-group my-5">
		<button
			class="btn btn-xs"
			class:btn-active={btnTimeFrame === 'All Time'}
			on:click={() => fetchTopTracks('long_term')}>All time</button
		>
		<button
			class="btn btn-xs"
			class:btn-active={btnTimeFrame === 'Last 6 Weeks'}
			on:click={() => fetchTopTracks('medium_term')}>Last 6 Weeks</button
		>
		<button
			class="btn btn-xs"
			class:btn-active={btnTimeFrame === 'Last 4 Weeks'}
			on:click={() => fetchTopTracks('short_term')}>Last 4 Weeks</button
		>
	</div>
</div>

<div
	class="py-3 grid gap-3
md:grid-flow-col md:grid-cols-2 md:grid-rows-[repeat(10,_minmax(0,_1fr))]
lg:grid-cols-3 lg:grid-rows-[repeat(7,_minmax(0,_1fr))]"
>
	{#each tracks as track, index}
		<!-- svelte-ignore a11y-click-events-have-key-events -->
		<label class="flex items-center space-x-3 cursor-pointer" on:click={() => selectedTrack = track} for="my-modal-6">
			<p class="w-1/12">{index + 1}.</p>

			<div class="avatar">
				<div class="w-12 h-12 bg-primary">
					{#if track.album.images[0]}
						<img src={track.album.images[0].url} alt={track.album.name} />
					{/if}
				</div>
			</div>

			<div class="text-left">
				<div class="font-bold">{track.name}</div>
				<div class="text-sm opacity-50">{track.artists[0].name}</div>
			</div>
		</label>
	{/each}
</div>

{#if selectedTrack}
	<SongModal {selectedTrack}/>
{/if}


<button class="btn" on:click={() => createPlaylist(btnTimeFrame)}>+ Create Playlist</button>
