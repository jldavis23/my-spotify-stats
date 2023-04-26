<script>
	//Imports
	import { onMount } from 'svelte';

	//Props
	export let accessToken;
	export let allTopTracksLong;
	export let allTopTracksShort;

	//Variables
	let audioFeaturesLong;
	let audioFeaturesShort;

	//Functions
	onMount(async () => {
		audioFeaturesLong = await getAudioFeatures(allTopTracksLong);
		audioFeaturesShort = await getAudioFeatures(allTopTracksShort);
		console.log(audioFeaturesLong);
		console.log(audioFeaturesShort);
	});

	const getAudioFeatures = async (tracks) => {
		let trackIds = tracks.map((track) => track.id);

		try {
			const result = await fetch(
				`https://api.spotify.com/v1/audio-features?ids=${trackIds.join()}`,
				{
					method: 'GET',
					headers: { Authorization: `Bearer ${accessToken}` }
				}
			);

			const trackData = await result.json();
			const trackFeatures = trackData.audio_features;
			return trackFeatures;
		} catch (err) {
			console.log(err);
		}
	};

	const formatAsPercentage = (num) => {
		return new Intl.NumberFormat('default', {
			style: 'percent',
			minimumFractionDigits: 2,
			maximumFractionDigits: 2
		}).format(num / 100);
	};

	const getAverage = (trackSet, feature) => {
		let sum = trackSet.reduce((acc, curr) => acc + curr[feature], 0);
		return formatAsPercentage((sum / trackSet.length) * 100);
	};

	const findTrackWithHighestScore = async (feature) => {
		let highest = 0;
		let trackWithHighestScore;
		audioFeaturesLong.forEach((track) => {
			if (track[feature] > highest) {
				highest = track[feature];
				trackWithHighestScore = track;
			}
		});
		console.log(trackWithHighestScore);

		try {
			const result = await fetch(`https://api.spotify.com/v1/tracks/${trackWithHighestScore.id}`, {
				method: 'GET',
				headers: { Authorization: `Bearer ${accessToken}` }
			});

			const trackData = await result.json();
			return trackData;
		} catch (err) {
			console.log(err);
		}
	};

	const findTrackWithLowestScore = async (feature) => {
		let lowest = 1;
		let trackWithLowestScore;
		audioFeaturesLong.forEach((track) => {
			if (track[feature] < lowest) {
				lowest = track[feature];
				trackWithLowestScore = track;
			}
		});
		console.log(trackWithLowestScore);

		try {
			const result = await fetch(`https://api.spotify.com/v1/tracks/${trackWithLowestScore.id}`, {
				method: 'GET',
				headers: { Authorization: `Bearer ${accessToken}` }
			});

			const trackData = await result.json();
			return trackData;
		} catch (err) {
			console.log(err);
		}
	};
</script>

<div class="p-10">
	<h2 class="text-4xl">Your Audio Features</h2>
	<p>paragraph explaining what this section is about</p>
</div>

{#if audioFeaturesLong && audioFeaturesShort}
	<div class="bg-[#D9EDDF] p-10 flex justify-between items-center gap-9 flex-col">
		<h3 class="text-3xl font-bold">Danceability</h3>
		<div class="grid sm:grid-cols-2 gap-9 text-center">
			<div>
				<p>CURRENT</p>
				<p class="text-5xl text-primary-focus">{getAverage(audioFeaturesShort, 'danceability')}</p>
			</div>
			<div>
				<p>ALL TIME</p>
				<p class="text-5xl text-primary-focus">{getAverage(audioFeaturesLong, 'danceability')}</p>
			</div>
		</div>
		<div class="grid sm:grid-cols-2 gap-6">
			{#await findTrackWithHighestScore('danceability')}
				loading
			{:then track}
				<div class="text-center flex flex-col items-center gap-3">
					<p class="">Most Danceable</p>

					<div class="avatar">
						<div class="w-24 h-24">
							<img src={track.album.images[0].url} alt={track.album.name} />
						</div>
					</div>

					<div class="">
						<div class="font-bold">{track.name}</div>
						<div class="text-sm opacity-50">{track.artists[0].name}</div>
					</div>
				</div>
			{/await}

			{#await findTrackWithLowestScore('danceability')}
				loading
			{:then track}
				<div class="text-center flex flex-col items-center gap-3">
					<p class="">Least Danceable</p>

					<div class="avatar">
						<div class="w-24 h-24">
							<img src={track.album.images[0].url} alt={track.album.name} />
						</div>
					</div>

					<div class="">
						<div class="font-bold">{track.name}</div>
						<div class="text-sm opacity-50">{track.artists[0].name}</div>
					</div>
				</div>
			{/await}
		</div>
	</div>
{/if}
