<script>
	import { onMount } from 'svelte';
	import { browser } from '$app/environment';
	import MyComponent from '../lib/components/MyComponent.svelte';
	import SecondComponent from '../lib/components/SecondComponent.svelte';

	const clientId = '';
	let accessToken;

	async function loginSpotify() {
		let code;
		if (browser) {
			const params = new URLSearchParams(window.location.search);
			code = params.get('code');
		}
		if (!code) {
			redirectToAuthCodeFlow(clientId);
		} else {
			console.log('trying to fetch profile');
			if (accessToken) {
				const profile = await fetchProfile(accessToken);
				console.log(profile);
			}
		}
	}

	onMount(async () => {
		let code;
		if (browser) {
			const params = new URLSearchParams(window.location.search);
			code = params.get('code');
		}
		if (!code) {
			//redirectToAuthCodeFlow(clientId);
		} else {
			accessToken = await getAccessToken(clientId, code);
			const profile = await fetchProfile(accessToken);
			console.log(profile);
		}
	});

	async function redirectToAuthCodeFlow(clientId) {
		const verifier = generateCodeVerifier(128);
		const challenge = await generateCodeChallenge(verifier);

		localStorage.setItem('verifier', verifier);

		const params = new URLSearchParams();
		params.append('client_id', clientId);
		params.append('response_type', 'code');
		params.append('redirect_uri', 'http://localhost:5490/');
		params.append(
			'scope',
			'user-read-private user-read-email user-top-read user-read-currently-playing'
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
		params.append('redirect_uri', 'http://localhost:5490/');
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
			//console.log(err)
		}
	}

	async function fetchTopTracks(token) {
		try {
			const result = await fetch('https://api.spotify.com/v1/me/top/tracks', {
				method: 'GET',
				headers: { Authorization: `Bearer ${token}` }
			});

			return await result.json();
		} catch (err) {
			//console.log(err)
		}
	}
</script>

<main>
	<div
		class="hero min-h-screen"
		style="background-image: url(https://images.unsplash.com/photo-1506157786151-b8491531f063?ixlib=rb-4.0.3&ixid=MnwxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8&auto=format&fit=crop&w=1470&q=80);"
	>
		<div class="hero-overlay bg-opacity-60" />
		<div class="hero-content text-center text-neutral-content">
			<div class="max-w-md">
				<h1 class="mb-5 text-5xl font-medium">My Spotify Stats</h1>
				<p class="mb-5">
					Learn more about your listening habits-- your top tracks, top artists, and more
				</p>
				{#if !accessToken}
				<button class="btn btn-primary" on:click={loginSpotify}>Login with Spotify</button>
				{:else}
				<p class="font-bold text-3xl">Here are your stats!</p>
				{/if}
			</div>
		</div>
	</div>

	<!-- {#await getAccessToken(clientId, code) then token}
        <MyComponent accessToken={token}/>
        <SecondComponent accessToken={token}/>
    {/await} -->
</main>
