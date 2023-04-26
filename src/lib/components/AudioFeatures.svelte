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
	}

	const getAverage = (trackSet, feature) => {
		let sum = trackSet.reduce((acc, curr) => acc + curr[feature], 0);
		return formatAsPercentage((sum / trackSet.length) * 100);
	};
</script>

<div class="p-10">
    <h2 class="text-4xl">Your Audio Features</h2>
    <p>paragraph explaining what this section is about</p>
</div>

{#if audioFeaturesLong && audioFeaturesShort}
	<div class="bg-[#D9EDDF] p-10 flex justify-between items-center gap-9 flex-col sm:flex-row">
		<h3 class="text-3xl font-bold">Danceability</h3>
        <div class="flex gap-9 text-center flex-col md:flex-row">
            <div>
                <p>CURRENT</p>
                <p class="text-5xl text-primary-focus">{getAverage(audioFeaturesShort, 'danceability')}</p>
            </div>
            <div>
                <p>ALL TIME</p>
                <p class="text-5xl text-primary-focus">{getAverage(audioFeaturesLong, 'danceability')}</p>
            </div>
        </div>
	</div>
{/if}
