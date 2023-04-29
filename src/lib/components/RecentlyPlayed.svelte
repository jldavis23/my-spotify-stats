<script>
	//Imports
	import { onMount } from 'svelte';
	import SongModal from './SongModal.svelte';
	import Loader from './Loader.svelte';

	//Props
	export let accessToken;

	//Variables
	let recentlyPlayed = [];
	let selectedTrack;

	//Functions
	onMount(async () => {
		recentlyPlayed = await getRecentlyPlayed();
	});

	const getRecentlyPlayed = async () => {
		try {
			const result = await fetch(`https://api.spotify.com/v1/me/player/recently-played`, {
				method: 'GET',
				headers: { Authorization: `Bearer ${accessToken}` }
			});

			const trackData = await result.json();
			return trackData.items;
		} catch (err) {
			console.log(err);
		}
	};
</script>

<h2 class="text-4xl">Your Recently Played Tracks</h2>

{#if recentlyPlayed.length > 0}
	<div class="grid gap-10 py-3">
		{#each recentlyPlayed as played}
			<!-- svelte-ignore a11y-click-events-have-key-events -->
			<label
				class="flex items-center space-x-3 cursor-pointer"
				on:click={() => (selectedTrack = played.track)}
				for="my-modal-6"
			>
				<div class="avatar">
					<div class="w-12 h-12 bg-primary">
						{#if played.track.album.images[0]}
							<img src={played.track.album.images[0].url} alt={played.track.album.name} />
						{/if}
					</div>
				</div>

				<div class="text-left">
					<div class="font-bold">{played.track.name}</div>
					<div class="text-sm opacity-50">{played.track.artists[0].name}</div>
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
