<script>
	//Imports
    import { onMount } from 'svelte'

	//Props
	export let accessToken;

	//Variables
	let tracks = []

	//Functions
	async function fetchTopTracks(token) {
		try {
			const result = await fetch('https://api.spotify.com/v1/me/top/tracks', {
				method: 'GET',
				headers: { Authorization: `Bearer ${token}` }
			});

            const trackData = await result.json();
			return trackData.items;
		} catch (err) {
			//console.log(err)
		}
	}

    // onMount(async () => {
    //     tracks = await fetchTopTracks(accessToken)
    //     console.log(tracks)
	// });

	tracks = [
		{
			name: 'For Youth',
			artists: [{name: 'BTS'}],
			album: {
				images: [{url: 'https://pngimg.com/uploads/square/square_PNG11.png'}]
			}
		}
	]

    
</script>

<main>
	{#each tracks as track}
		<p>{track.name}</p>
	{/each}
</main>
