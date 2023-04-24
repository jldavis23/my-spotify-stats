<script>
    //Imports
    import { onMount } from 'svelte'
    import { topTracksLong } from './../store/store.js'

    //Props
    export let accessToken

    //Variables
    
    let trackIds = []

    //Functions
    onMount(async () => {
        let listOfTracks = $topTracksLong
        console.log($topTracksLong)
        trackIds = listOfTracks.map((track) => track.id);
        console.log(await fetchMoreTrackInfo())
    })

    const fetchMoreTrackInfo = async () => {
        try {
			const result = await fetch(`https://api.spotify.com/v1/tracks/?ids=${trackIds.join()}`, {
				method: 'GET',
				headers: { Authorization: `Bearer ${accessToken}` }
			});
		} catch (err) {
			console.log(err);
		}
    }
</script>

<div>HELLO GENRES</div>