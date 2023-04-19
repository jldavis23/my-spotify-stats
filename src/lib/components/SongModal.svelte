<script>
	export let selectedTrack;
</script>

<input type="checkbox" id="my-modal-6" class="modal-toggle" />
<div class="modal modal-bottom sm:modal-middle">
	<div class="modal-box">
		<h3 class="font-bold text-lg">{selectedTrack.name}</h3>
		<p class="text-sm opacity-50">By {selectedTrack.artists[0].name}</p>
		<p class="py-4">Album: {selectedTrack.album.name}</p>
		<p class="py-4">Duration: {selectedTrack.duration_ms}</p>
		<div class="modal-action">
			<label for="my-modal-6" class="btn">Yay!</label>
		</div>
	</div>
</div>
