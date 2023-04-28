<script>
	//Props
	export let selectedArtist;
	export let accessToken;
	export let userCountry;

	//Functions
	const findArtistTopTracks = async (artist) => {
		try {
			const result = await fetch(
				`https://api.spotify.com/v1/artists/${selectedArtist.id}/top-tracks?market=${userCountry}`,
				{
					method: 'GET',
					headers: { Authorization: `Bearer ${accessToken}` }
				}
			);

			const artistData = await result.json();
			return artistData.tracks.slice(0, 5);
		} catch (err) {
			console.log(err);
		}
	};
</script>

<input type="checkbox" id="my-modal-5" class="modal-toggle" />
<div class="modal modal-bottom sm:modal-middle">
	<div class="modal-box">
		<div class="flex justify-between">
			<div class="flex gap-3">
				<div class="w-16 h-16 bg-primary">
					{#if selectedArtist.images[1]}
						<img src={selectedArtist.images[1].url} class="w-16 h-16" />
					{/if}
				</div>
				<div>
					<h3 class="font-bold text-lg">{selectedArtist.name}</h3>
				</div>
			</div>

			<label for="my-modal-5" class="btn btn-square">
				<svg
					xmlns="http://www.w3.org/2000/svg"
					class="h-6 w-6"
					fill="none"
					viewBox="0 0 24 24"
					stroke="currentColor"
					><path
						stroke-linecap="round"
						stroke-linejoin="round"
						stroke-width="2"
						d="M6 18L18 6M6 6l12 12"
					/></svg
				></label
			>
		</div>

		<div class="py-3">
			<p class="text-primary-focus">GENRES</p>
			<ul>
				{#if selectedArtist.genres.length < 1}
					<li>No genres specified for this artist</li>
				{:else}
					{#each selectedArtist.genres as genre}
						<li class="list-disc mx-4">{genre}</li>
					{/each}
				{/if}
			</ul>
		</div>

		<div class="py-3">
			<p class="text-primary-focus">TOP TRACKS</p>
			<ul>
				{#await findArtistTopTracks(selectedArtist) then artistTopTracks}
					{#each artistTopTracks as track}
						<li class="list-disc mx-4">
							<a href={track.external_urls.spotify} target="_blank" class="hover:link">{track.name}</a>
						</li>
					{/each}
				{/await}
			</ul>
		</div>

		<a
			href={selectedArtist.external_urls.spotify}
			target="_blank"
			class="btn btn-sm btn-primary my-3">View on Spotify</a
		>
	</div>
</div>
