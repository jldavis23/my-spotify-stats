<script>
	//Imports
	import { onMount } from 'svelte';

	//Props
	export let accessToken;
	export let allTopArtistsShort;

	//Variables
    let allArtistRecs = []

	//Functions
    onMount(async () => {
        await getRelatedArtists()
    })

    const getRelatedArtists = async () => {
        let random = Math.floor(Math.random() * (allTopArtistsShort.length));
        let artistId = allTopArtistsShort[random].id

        try {
			const result = await fetch(
				`https://api.spotify.com/v1/artists/${artistId}/related-artists`,
				{
					method: 'GET',
					headers: { Authorization: `Bearer ${accessToken}` }
				}
			);

			const data = await result.json();
            let artistRecs = data.artists
            let filteredArtists = []

            artistRecs.forEach((rec) => {
				if (!allTopArtistsShort.some((artist) => artist.id === rec.id)) {
					filteredArtists.push(rec);
				}
			});
            
            allArtistRecs = artistRecs
			console.log(allArtistRecs)
			
		} catch (err) {
			console.log(err);
		}
    }
</script>


<h2 class="text-4xl ">Artist Recommendations</h2>

<button class="btn my-6 btn-sm" on:click={getRelatedArtists}>Refresh</button>
	
<div class="py-3 grid gap-6 md:grid-cols-2 lg:grid-cols-3">
	{#each allArtistRecs as artist}
		<div class="tooltip tooltip-primary" data-tip="Album:  | Duration:">
			<div class="flex items-center space-x-3 cursor-default p-2">

				<div class="avatar">
					<div class="w-12 h-12 bg-primary">
						{#if artist.images[0]}
							<img src={artist.images[0].url} />
						{/if}
					</div>
				</div>

				<div class="text-left">
					<div class="font-bold">{artist.name}</div>
				</div>
			</div>
		</div>
	{/each}
</div>

<!-- {#if selectedTrack}
	<SongModal {selectedTrack}/>
{/if} -->

