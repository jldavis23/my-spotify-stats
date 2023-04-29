<script>
	import { onMount } from 'svelte';
	import { browser } from '$app/environment';
	import { fly } from 'svelte/transition';
	import { goto } from '$app/navigation';
	//Components
	import TopTracks from '../../lib/components/TopTracks.svelte';
	import TopArtists from '../../lib/components/TopArtists.svelte';
	import Genres from '../../lib/components/Genres.svelte';
	import Decade from '../../lib/components/Decade.svelte';
	import AudioFeatures from '../../lib/components/AudioFeatures.svelte';
	import TrackRecs from '../../lib/components/TrackRecs.svelte';
	import RelatedArtists from '../../lib/components/RelatedArtists.svelte';
	import ObscureTracks from '../../lib/components/ObscureTracks.svelte';
	import ObscureArtists from '../../lib/components/ObscureArtists.svelte';
	import RecentlyPlayed from '../../lib/components/RecentlyPlayed.svelte';
	import Loader from '../../lib/components/Loader.svelte';

	const clientId = '00867bbf4f294a7ea0b4f12c47190c0c';
	let accessToken = null;
	let code;
	let isAuthenticated = false;
	let profile;
	let allTopTracksLong;
	let allTopTracksShort;
	let allTopArtistsLong;
	let allTopArtistsShort;
	let active = 'topItems';
	let currentlyPlaying;

	onMount(async () => {
		if (browser) {
			const params = new URLSearchParams(window.location.search);
			code = params.get('code');
		}
		if (!code) {
			redirectToAuthCodeFlow(clientId);
		} else {
			isAuthenticated = true;
			accessToken = await getAccessToken(clientId, code);
			profile = await fetchProfile(accessToken);
			await getCurrentlyPlaying();

			allTopTracksLong = await fetchAllTopTracks('long_term');
			allTopTracksShort = await fetchAllTopTracks('short_term');
			allTopArtistsLong = await fetchAllTopArtists('long_term');
			allTopArtistsShort = await fetchAllTopArtists('short_term');

			goto('/stats')
		}
	});

	$: active, getCurrentlyPlaying();

	async function redirectToAuthCodeFlow(clientId) {
		const verifier = generateCodeVerifier(128);
		const challenge = await generateCodeChallenge(verifier);

		localStorage.setItem('verifier', verifier);

		const params = new URLSearchParams();
		params.append('client_id', clientId);
		params.append('response_type', 'code');
		params.append('redirect_uri', 'https://my-spotify-stats.vercel.app/stats');
		params.append(
			'scope',
			'user-read-private user-read-email user-top-read user-read-currently-playing user-read-recently-played playlist-modify-public playlist-modify-private'
		);
		params.append('code_challenge_method', 'S256');
		params.append('code_challenge', challenge);

		document.location = `https://accounts.spotify.com/authorize?${params.toString()}`;
	}

	function generateCodeVerifier(length) {
		let text = '';
		let possible = 'ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789';

		for (let i = 0; i < length; i++) {
			text += possible.charAt(Math.floor(Math.random() * possible.length));
		}
		return text;
	}

	async function generateCodeChallenge(codeVerifier) {
		const data = new TextEncoder().encode(codeVerifier);
		const digest = await window.crypto.subtle.digest('SHA-256', data);
		return btoa(String.fromCharCode.apply(null, [...new Uint8Array(digest)]))
			.replace(/\+/g, '-')
			.replace(/\//g, '_')
			.replace(/=+$/, '');
	}

	async function getAccessToken(clientId, code) {
		const verifier = localStorage.getItem('verifier');

		const params = new URLSearchParams();
		params.append('client_id', clientId);
		params.append('grant_type', 'authorization_code');
		params.append('code', code);
		params.append('redirect_uri', 'https://my-spotify-stats.vercel.app/stats');
		params.append('code_verifier', verifier);

		try {
			const result = await fetch('https://accounts.spotify.com/api/token', {
				method: 'POST',
				headers: { 'Content-Type': 'application/x-www-form-urlencoded' },
				body: params
			});
			const { access_token } = await result.json();
			return access_token;
		} catch (err) {
			console.log(err);
		}
	}

	async function fetchProfile(token) {
		try {
			const result = await fetch('https://api.spotify.com/v1/me', {
				method: 'GET',
				headers: { Authorization: `Bearer ${token}` }
			});

			return await result.json();
		} catch (err) {
			console.log(err);
		}
	}

	async function fetchAllTopTracks(timeFrame) {
		try {
			const result = await fetch(
				`https://api.spotify.com/v1/me/top/tracks?limit=50&offset=0&time_range=${timeFrame}`,
				{
					method: 'GET',
					headers: { Authorization: `Bearer ${accessToken}` }
				}
			);

			const trackData = await result.json();
			const tracks = trackData.items;
			return tracks;
		} catch (err) {
			console.log(err);
		}
	}

	async function fetchAllTopArtists(timeFrame) {
		try {
			const result = await fetch(
				`https://api.spotify.com/v1/me/top/artists?limit=50&offset=0&time_range=${timeFrame}`,
				{
					method: 'GET',
					headers: { Authorization: `Bearer ${accessToken}` }
				}
			);

			const artistData = await result.json();
			const artists = artistData.items;
			return artists;
		} catch (err) {
			console.log(err);
		}
	}

	async function getCurrentlyPlaying() {
		try {
			const result = await fetch(
				`https://api.spotify.com/v1/me/player/currently-playing?market=${profile.country}`,
				{
					method: 'GET',
					headers: { Authorization: `Bearer ${accessToken}` }
				}
			);

			const data = await result.json();
			currentlyPlaying = data.item;
		} catch (err) {
			currentlyPlaying = 'none';
		}
	}

	const navigate = (section) => {
		active = null;
		setTimeout(() => {
			active = section;
		}, 300);
	};
</script>

{#if !isAuthenticated}
	<div class="h-screen flex justify-center items-center">
		<Loader />
	</div>
{:else if profile && allTopTracksLong && allTopArtistsLong && allTopTracksShort && allTopArtistsShort && currentlyPlaying}
	<div class="bg-neutral text-neutral-content">
		<div class="navbar px-5">
			<div class="flex-1">
				<div class="dropdown text-primary-content">
					<!-- svelte-ignore a11y-no-noninteractive-tabindex -->
					<!-- svelte-ignore a11y-label-has-associated-control -->
					<label tabindex="0" class="btn m-1 sm:hidden"
						><svg
							xmlns="http://www.w3.org/2000/svg"
							fill="none"
							viewBox="0 0 24 24"
							class="inline-block w-5 h-5 stroke-current"
							><path
								stroke-linecap="round"
								stroke-linejoin="round"
								stroke-width="2"
								d="M4 6h16M4 12h16M4 18h16"
							/></svg
						></label
					>
					<!-- svelte-ignore a11y-no-noninteractive-tabindex -->
					<ul tabindex="0" class="dropdown-content menu p-2 shadow bg-primary rounded-box w-52">
						<li>
							<a
								href="#top"
								class:active={active === 'topItems'}
								on:click={() => navigate('topItems')}>Top Items</a
							>
						</li>
						<li>
							<a
								href="#top"
								class:active={active === 'audioFeatures'}
								on:click={() => navigate('audioFeatures')}>Audio Analysis</a
							>
						</li>
						<li>
							<a
								href="#top"
								class:active={active === 'obscure'}
								on:click={() => navigate('obscure')}>Most Obscure</a
							>
						</li>
						<li>
							<a
								href="#top"
								class:active={active === 'recommendations'}
								on:click={() => navigate('recommendations')}>Recommendations</a
							>
						</li>
					</ul>
				</div>
				<p class="normal-case text-xl">My Spotify Stats</p>
			</div>
			<div class="flex-none gap-2">
				<p>{profile.display_name}</p>
				<div class="dropdown dropdown-end">
					<!-- svelte-ignore a11y-no-noninteractive-tabindex -->
					<!-- svelte-ignore a11y-label-has-associated-control -->
					<label tabindex="0" class="btn btn-ghost btn-circle avatar">
						<div class="w-10 rounded-full">
							<img src={profile.images[0].url} alt="avatar" />
						</div>
					</label>
					<!-- svelte-ignore a11y-no-noninteractive-tabindex -->
					<ul
						tabindex="0"
						class="mt-3 p-2 shadow menu menu-compact dropdown-content bg-base-100 rounded-box text-primary-content w-52"
					>
						<p class="p-3 text-primary-focus">
							{#if currentlyPlaying === 'none'}
								Currently Playing: none
							{:else}
								Currently Playing: {currentlyPlaying.name}
								{#each currentlyPlaying.artists as artist}
									<p class="text-neutral opacity-50">{artist.name}</p>
								{/each}
							{/if}
						</p>

						<li><a href="#top" on:click={() => navigate('recentlyPlayed')}>Recently Played</a></li>
					</ul>
				</div>
			</div>
		</div>
	</div>

	<main class="sm:flex">
		<ul
			class="menu menu-horizontal hidden sm:block sm:menu-vertical bg-primary w-full sm:w-60 p-3 sm:sticky sm:top-0 sm:h-screen"
		>
			<li>
				<a href="#top" class:active={active === 'topItems'} on:click={() => navigate('topItems')}
					>Top Items</a
				>
			</li>
			<li>
				<a
					href="#top"
					class:active={active === 'audioFeatures'}
					on:click={() => navigate('audioFeatures')}>Audio Analysis</a
				>
			</li>
			<li>
				<a href="#top" class:active={active === 'obscure'} on:click={() => navigate('obscure')}
					>Most Obscure</a
				>
			</li>
			<li>
				<a
					href="#top"
					class:active={active === 'recommendations'}
					on:click={() => navigate('recommendations')}>Recommendations</a
				>
			</li>
		</ul>

		<div class="w-full">
			{#if active === 'topItems'}
				<div transition:fly={{ duration: 300, y: 100 }}>
					<div class="p-10 bg-[#D9EDDF]">
						<div class="max-w-[1400px]">
							<p>tracks you can't get enough of right now</p>
							<TopTracks {accessToken} userId={profile.id} />
						</div>
					</div>

					<div class="p-10 max-w-[1400px]">
						<p>these artists dominate your playlists</p>
						<TopArtists {accessToken} userCountry={profile.country} />
					</div>

					<div class="bg-[#EEFDF2]">
						<div class="grid md:grid-cols-2 max-w-[1400px]">
							<div class="p-10 bg-[#D9EDDF]">
								<Genres {allTopArtistsLong} {allTopArtistsShort} />
							</div>
							<div class="p-10"><Decade {allTopTracksLong} {allTopTracksShort} /></div>
						</div>
					</div>
				</div>
			{/if}

			{#if active === 'audioFeatures'}
				<div transition:fly={{ duration: 300, y: 100 }}>
					<AudioFeatures {accessToken} {allTopTracksLong} {allTopTracksShort} />
				</div>
			{/if}

			{#if active === 'obscure'}
				<div transition:fly={{ duration: 300, y: 100 }}>
					<div class="bg-[#D9EDDF]">
						<div class="p-10 max-w-[1400px]">
							<ObscureTracks {accessToken} {allTopTracksLong} {allTopTracksShort} />
						</div>
					</div>

					<div class="p-10 max-w-[1400px]">
						<ObscureArtists
							{allTopArtistsShort}
							{allTopArtistsLong}
							userCountry={profile.country}
							{accessToken}
						/>
					</div>
				</div>
			{/if}

			{#if active === 'recommendations'}
				<div transition:fly={{ duration: 300, y: 100 }}>
					<div class="p-10 max-w-[1400px]">
						<TrackRecs {accessToken} {allTopTracksShort} userId={profile.id} />
					</div>

					<div class="bg-[#D9EDDF]">
						<div class="p-10 max-w-[1400px]">
							<RelatedArtists {accessToken} {allTopArtistsShort} userCountry={profile.country} />
						</div>
					</div>
				</div>
			{/if}

			{#if active === 'recentlyPlayed'}
				<div class="p-10 max-w-[1400px]" transition:fly={{ duration: 300, y: 100 }}>
					<RecentlyPlayed {accessToken} />
				</div>
			{/if}
		</div>
	</main>
{:else}
	<div class="h-screen flex justify-center items-center">
		<Loader />
	</div>
{/if}

<style>
	.active {
		background-color: #59aa6e;
	}
</style>
