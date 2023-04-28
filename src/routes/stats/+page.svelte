<script>
	import { onMount } from 'svelte';
	import { browser } from '$app/environment';
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

	// delete later
	// profile = {
	// 	display_name: 'George',
	// 	images: [{ url: '' }]
	// };

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

			allTopTracksLong = await fetchAllTopTracks('long_term');
			allTopTracksShort = await fetchAllTopTracks('short_term');
			allTopArtistsLong = await fetchAllTopArtists('long_term');
			allTopArtistsShort = await fetchAllTopArtists('short_term');
		}
	});

	async function redirectToAuthCodeFlow(clientId) {
		const verifier = generateCodeVerifier(128);
		const challenge = await generateCodeChallenge(verifier);

		localStorage.setItem('verifier', verifier);

		const params = new URLSearchParams();
		params.append('client_id', clientId);
		params.append('response_type', 'code');
		params.append('redirect_uri', 'http://localhost:5490/stats');
		params.append(
			'scope',
			'user-read-private user-read-email user-top-read user-read-currently-playing playlist-modify-public playlist-modify-private'
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
		params.append('redirect_uri', 'http://localhost:5490/stats');
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
</script>

{#if !isAuthenticated}
	<h1>LOADING</h1>
{:else if profile && allTopTracksLong && allTopArtistsLong && allTopTracksShort && allTopArtistsShort}
	<div class="bg-neutral text-neutral-content">
		<div class="navbar">
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
						<li><a class:active={active === 'topItems'} on:click={() => active = 'topItems'}>Top Items</a></li>
						<li><a class:active={active === 'audioFeatures'} on:click={() => active = 'audioFeatures'}>Audio Analysis</a></li>
						<li><a class:active={active === 'obscure'} on:click={() => active = 'obscure'}>Most Obscure</a></li>
						<li><a class:active={active === 'recommendations'} on:click={() => active = 'recommendations'}>Recommendations</a></li>
					</ul>
				</div>
				<p class="normal-case text-xl">My Spotify Stats</p>
			</div>
			<div class="flex-none gap-2">
				<p>{profile.display_name}</p>
				<div class="avatar">
					<div class="w-10 rounded-full">
						<img src={profile.images[0].url} alt="avatar" />
					</div>
				</div>
			</div>
		</div>
	</div>

	<main class="sm:flex max-w-[1400px]">
		<ul
			class="menu menu-horizontal hidden sm:block sm:menu-vertical bg-primary w-full sm:w-60 p-3 sm:sticky sm:top-0 sm:h-screen"
		>
			<li><a class:active={active === 'topItems'} on:click={() => active = 'topItems'}>Top Items</a></li>
			<li><a class:active={active === 'audioFeatures'} on:click={() => active = 'audioFeatures'}>Audio Analysis</a></li>
			<li><a class:active={active === 'obscure'} on:click={() => active = 'obscure'}>Most Obscure</a></li>
			<li><a class:active={active === 'recommendations'} on:click={() => active = 'recommendations'}>Recommendations</a></li>
		</ul>

		<div class="w-full">
			{#if active === 'topItems'}
				<div id="" class="p-10 bg-[#D9EDDF]">
					<p>tracks you can't get enough of right now</p>
					<TopTracks {accessToken} userId={profile.id} />
				</div>

				<div class="p-10">
					<p>these artists dominate your playlists</p>
					<TopArtists {accessToken} />
				</div>

				<div class="grid md:grid-cols-2">
					<div class="p-10 bg-[#D9EDDF]"><Genres {allTopArtistsLong} {allTopArtistsShort} /></div>
					<div class="p-10 bg-[#EEFDF2]"><Decade {allTopTracksLong} {allTopTracksShort} /></div>
				</div>
			{/if}

			{#if active === 'audioFeatures'}
				<div id="" class="">
					<AudioFeatures {accessToken} {allTopTracksLong} {allTopTracksShort} />
				</div>
			{/if}

			{#if active === 'obscure'}
				<div class="p-10 bg-[#D9EDDF]">
					<ObscureTracks {accessToken} {allTopTracksLong} {allTopTracksShort}/>
				</div>

				<div class="p-10">
					<ObscureArtists {allTopArtistsShort} {allTopArtistsLong}/>
				</div>
			{/if}

			{#if active === 'recommendations'}
				<div class="p-10">
					<TrackRecs {accessToken} {allTopTracksShort} userId={profile.id} />
				</div>

				<div class="p-10 bg-[#D9EDDF]">
					<RelatedArtists {accessToken} {allTopArtistsShort} />
				</div>
			{/if}
		</div>
	</main>
{/if}

<style>
	.active {
		background-color: #59aa6e;
	}
</style>