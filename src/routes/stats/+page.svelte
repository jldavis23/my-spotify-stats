<script>
	import { onMount } from 'svelte';
	import { browser } from '$app/environment';
	import TopTracks from '../../lib/components/TopTracks.svelte';

	const clientId = '';
	let accessToken = null;
	let code;
	let isAuthenticated = false;
	let profile;

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
</script>

<main class="max-w-5xl m-auto p-10">
	{#if !isAuthenticated}
		<p>loading</p>
	{:else}
		{#if profile}
			<p>here's your stats!</p>
			<TopTracks {accessToken} userId={profile.id} />
		{/if}
		<!-- {#await getAccessToken(clientId, code)}
		<p>LOADING...</p>
	{:then token}
		<p>here's your stats!</p>
		<TopTracks accessToken={token} />
	{/await} -->
	{/if}

	<!-- <TopTracks accessToken={123}/> -->
</main>
