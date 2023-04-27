<script>
	//Imports
	import { onMount } from 'svelte';

	//Props
	export let allTopTracksLong;
	export let allTopTracksShort;

	//Variables
	let topObscureTracks = [];

	//Functions
	const findObscureTracks = () => {
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

		topObscureTracks = everyTopTrack.slice(0, 6);
	};

	onMount(() => {
		findObscureTracks();
	});
</script>

<h1>HELLO</h1>

<div class="py-3 grid gap-6 md:grid-cols-2 lg:grid-cols-3">
	{#each topObscureTracks as track}
		<div class="tooltip tooltip-primary" data-tip="Album: {track.album.name} | Duration: ">
			<div class="flex items-center space-x-3 cursor-default">
				<div class="avatar">
					<div class="w-12 h-12">
						<img src={track.album.images[0].url} alt={track.album.name} />
					</div>
				</div>

				<div class="text-left">
					<div class="font-bold">{track.name}</div>
					<div class="text-sm opacity-50">{track.artists[0].name}</div>
				</div>
			</div>
		</div>
	{/each}
</div>
