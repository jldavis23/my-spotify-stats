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
		audioFeaturesShort.forEach((track) => {
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
		audioFeaturesShort.forEach((track) => {
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
	<h2 class="text-4xl">Your Audio Analysis</h2>
	<p>
		Spotify measures audio features such as acousticness, danceability, and energy of individual
		tracks. Lets analyze your top tracks and find your audio feature scores!
	</p>
</div>

{#if audioFeaturesLong && audioFeaturesShort}
	<div class="bg-[#D9EDDF] p-10 flex justify-between gap-9 flex-col items-center sm:items-start">
		<div class="text-center sm:text-left">
			<h3 class="text-3xl font-bold">Danceability</h3>
			<p>
				The "danceability" of a track is measured through a combination of elements such as tempo,
				rhythm stability, beat strength, and overall regularity. The higher the percentage, the more
				"danceable" your music is!
			</p>
		</div>

		<div class="flex flex-col md:flex-row gap-10">
			<div class="flex flex-col sm:flex-row md:flex-col gap-5">
				<div class="text-center sm:text-left">
					<p>CURRENT</p>
					<p class="text-5xl text-primary-focus">
						{getAverage(audioFeaturesShort, 'danceability')}
					</p>
				</div>
				<div class="text-center sm:text-left">
					<p>ALL TIME</p>
					<p class="text-5xl text-primary-focus">{getAverage(audioFeaturesLong, 'danceability')}</p>
				</div>
			</div>

			<div class="flex flex-col sm:flex-row gap-5">
				{#await findTrackWithHighestScore('danceability')}
					loading
				{:then track}
					<div class="text-center">
						<p class="">Most Danceable</p>

						<div class="avatar">
							<div class="w-24 h-24 b-primary">
								{#if track.album.images[0]}
									<img src={track.album.images[0].url} alt={track.album.name} />
								{/if}
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
					<div class="text-center">
						<p class="">Least Danceable</p>

						<div class="avatar">
							<div class="w-24 h-24 b-primary">
								{#if track.album.images[0]}
									<img src={track.album.images[0].url} alt={track.album.name} />
								{/if}
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
	</div>

	<div class="p-10 bg-[#EEFDF2] flex justify-between gap-9 flex-col items-center sm:items-end">
		<div class="text-center sm:text-right">
			<h3 class="text-3xl font-bold">Acousticness</h3>
			<p>The higher the percentage, the more acoustic your music is overall.</p>
		</div>

		<div class="flex flex-col md:flex-row gap-10">
			<div class="flex flex-col sm:flex-row md:flex-col gap-5">
				<div class="text-center sm:text-left">
					<p>CURRENT</p>
					<p class="text-5xl text-primary-focus">
						{getAverage(audioFeaturesShort, 'acousticness')}
					</p>
				</div>
				<div class="text-center sm:text-left">
					<p>ALL TIME</p>
					<p class="text-5xl text-primary-focus">{getAverage(audioFeaturesLong, 'acousticness')}</p>
				</div>
			</div>

			<div class="flex flex-col sm:flex-row gap-5">
				{#await findTrackWithHighestScore('acousticness')}
					loading
				{:then track}
					<div class="text-center">
						<p class="">Most Acoustic</p>

						<div class="avatar">
							<div class="w-24 h-24 bg-primary">
								{#if track.album.images[0]}
									<img src={track.album.images[0].url} alt={track.album.name} />
								{/if}
							</div>
						</div>

						<div class="">
							<div class="font-bold">{track.name}</div>
							<div class="text-sm opacity-50">{track.artists[0].name}</div>
						</div>
					</div>
				{/await}

				{#await findTrackWithLowestScore('acousticness')}
					loading
				{:then track}
					<div class="text-center">
						<p class="">Least Acoustic</p>

						<div class="avatar">
							<div class="w-24 h-24 b-primary">
								{#if track.album.images[0]}
									<img src={track.album.images[0].url} alt={track.album.name} />
								{/if}
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
	</div>

	<div class="p-10 flex justify-between gap-9 flex-col items-center sm:items-start">
		<div class="text-center sm:text-left">
			<h3 class="text-3xl font-bold">Energy</h3>
			<p>
				The energy of a track is determined by measuring intensity and activity. The higher your
				percentage is, the more energetic your music is.
			</p>
		</div>

		<div class="flex flex-col md:flex-row gap-10">
			<div class="flex flex-col sm:flex-row md:flex-col gap-5">
				<div class="text-center sm:text-left">
					<p>CURRENT</p>
					<p class="text-5xl text-primary-focus">
						{getAverage(audioFeaturesShort, 'energy')}
					</p>
				</div>
				<div class="text-center sm:text-left">
					<p>ALL TIME</p>
					<p class="text-5xl text-primary-focus">{getAverage(audioFeaturesLong, 'energy')}</p>
				</div>
			</div>

			<div class="flex flex-col sm:flex-row gap-5">
				{#await findTrackWithHighestScore('energy')}
					loading
				{:then track}
					<div class="text-center">
						<p class="">Most Energy</p>

						<div class="avatar">
							<div class="w-24 h-24 bg-primary">
								{#if track.album.images[0]}
									<img src={track.album.images[0].url} alt={track.album.name} />
								{/if}
							</div>
						</div>

						<div class="">
							<div class="font-bold">{track.name}</div>
							<div class="text-sm opacity-50">{track.artists[0].name}</div>
						</div>
					</div>
				{/await}

				{#await findTrackWithLowestScore('energy')}
					loading
				{:then track}
					<div class="text-center">
						<p class="">Least Energy</p>

						<div class="avatar">
							<div class="w-24 h-24 bg-primary">
								{#if track.album.images[0]}
									<img src={track.album.images[0].url} alt={track.album.name} />
								{/if}
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
	</div>

	<div class="bg-[#D9EDDF] p-10 flex justify-between gap-9 flex-col items-center sm:items-end">
		<div class="text-center sm:text-right">
			<h3 class="text-3xl font-bold">Instrumentalness</h3>
			<p>
				A low percentage indicates the music your listen to is mostly vocal. A higher percentage
				indicates your music is mostly instrumental.
			</p>
		</div>

		<div class="flex flex-col md:flex-row gap-10">
			<div class="flex flex-col sm:flex-row md:flex-col gap-5">
				<div class="text-center sm:text-left">
					<p>CURRENT</p>
					<p class="text-5xl text-primary-focus">
						{getAverage(audioFeaturesShort, 'instrumentalness')}
					</p>
				</div>
				<div class="text-center sm:text-left">
					<p>ALL TIME</p>
					<p class="text-5xl text-primary-focus">
						{getAverage(audioFeaturesLong, 'instrumentalness')}
					</p>
				</div>
			</div>

			<div class="flex flex-col sm:flex-row gap-5">
				{#await findTrackWithHighestScore('instrumentalness')}
					loading
				{:then track}
					<div class="text-center">
						<p class="">Most Instrumental</p>

						<div class="avatar">
							<div class="w-24 h-24 bg-primary">
								{#if track.album.images[0]}
									<img src={track.album.images[0].url} alt={track.album.name} />
								{/if}
							</div>
						</div>

						<div class="">
							<div class="font-bold">{track.name}</div>
							<div class="text-sm opacity-50">{track.artists[0].name}</div>
						</div>
					</div>
				{/await}

				{#await findTrackWithLowestScore('instrumentalness')}
					loading
				{:then track}
					<div class="text-center">
						<p class="">Least Instrumental</p>

						<div class="avatar">
							<div class="w-24 h-24 bg-primary">
								{#if track.album.images[0]}
									<img src={track.album.images[0].url} alt={track.album.name} />
								{/if}
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
	</div>

	<div class="bg-[#EEFDF2] p-10 flex justify-between gap-9 flex-col items-center sm:items-start">
		<div class="text-center sm:text-left">
			<h3 class="text-3xl font-bold">Valence</h3>
			<p>
				The valence of a track refers to its musical positiveness. The higher your percentage, the
				more positive/happy your music is.
			</p>
		</div>

		<div class="flex flex-col md:flex-row gap-10">
			<div class="flex flex-col sm:flex-row md:flex-col gap-5">
				<div class="text-center sm:text-left">
					<p>CURRENT</p>
					<p class="text-5xl text-primary-focus">
						{getAverage(audioFeaturesShort, 'valence')}
					</p>
				</div>
				<div class="text-center sm:text-left">
					<p>ALL TIME</p>
					<p class="text-5xl text-primary-focus">{getAverage(audioFeaturesLong, 'valence')}</p>
				</div>
			</div>

			<div class="flex flex-col sm:flex-row gap-5">
				{#await findTrackWithHighestScore('valence')}
					loading
				{:then track}
					<div class="text-center">
						<p class="">Most Valent</p>

						<div class="avatar">
							<div class="w-24 h-24 bg-primary">
								{#if track.album.images[0]}
									<img src={track.album.images[0].url} alt={track.album.name} />
								{/if}
							</div>
						</div>

						<div class="">
							<div class="font-bold">{track.name}</div>
							<div class="text-sm opacity-50">{track.artists[0].name}</div>
						</div>
					</div>
				{/await}

				{#await findTrackWithLowestScore('valence')}
					loading
				{:then track}
					<div class="text-center">
						<p class="">Least Valent</p>

						<div class="avatar">
							<div class="w-24 h-24 bg-primary">
								{#if track.album.images[0]}
									<img src={track.album.images[0].url} alt={track.album.name} />
								{/if}
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
	</div>
{/if}
