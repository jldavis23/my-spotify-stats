<script>
	//Props
	export let selectedTrack;

	//Functions
	const convertToMinSec = (duration) => {
		if (duration > 60000) {
			let totalMinutes = duration / 1000 / 60;
			let seconds = Math.round((totalMinutes % Math.floor(totalMinutes)) * 60);
			let minutes = Math.floor(totalMinutes);
			if (seconds < 10) {
				return `${minutes}:0${seconds}`;
			} else {
				return `${minutes}:${seconds}`;
			}
		} else if (duration > 999) {
			return `0:${Math.round(duration / 1000)}`
		} else {
			return `0:0${Math.round(duration / 1000)}`
		}
	};
</script>

<input type="checkbox" id="my-modal-6" class="modal-toggle" />
<div class="modal modal-bottom sm:modal-middle">
	<div class="modal-box">
		<div class="flex justify-between">
			<div class="flex gap-3">
				<div class="w-16 h-16 bg-primary">
					{#if selectedTrack.album.images[0]}
						<img src={selectedTrack.album.images[0].url} class="w-16 h-16" />
					{/if}
				</div>
				<div>
					<h3 class="font-bold text-lg">{selectedTrack.name}</h3>
					<p class="text-sm opacity-50">
						{#each selectedTrack.artists as artist}
							<p>{artist.name}</p>
						{/each}
					</p>
				</div>
			</div>

			<label for="my-modal-6" class="btn btn-square">
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
			<p class="text-primary-focus">ALBUM</p>
			<p>{selectedTrack.album.name}</p>
		</div>
		<div class="py-3">
			<p class="text-primary-focus">DURATION</p>
			<p>{convertToMinSec(selectedTrack.duration_ms)}</p>
		</div>
		<div class="py-3">
			<p class="text-primary-focus">RELEASE DATE</p>
			<p>{selectedTrack.album.release_date}</p>
		</div>

		<a
			href={selectedTrack.external_urls.spotify}
			target="_blank"
			class="btn btn-sm btn-primary my-3">Listen on Spotify</a
		>

		<div class="" />
	</div>
</div>
