<script>
    import { onMount } from 'svelte'

	export let accessToken;

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

    onMount(async () => {
        const topTracks = await fetchTopTracks(accessToken)
        console.log(topTracks)
	});

    
</script>

<main>
	<p>what's up</p>
</main>
