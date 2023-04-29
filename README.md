# My Spotify Stats
App that displays statistics about a Spotify user's listening habits. Like Spotify Unwrapped, but any time you want it.

Tools and Frameworks: Built using SvelteKit and Spotify's Web API

**LIVE SITE: (https://my-spotify-stats.vercel.app/)**

## Requirements
Click on a link to view the code
- Use the Spotify Web API to [fetch a user’s profile](https://github.com/jldavis23/my-spotify-stats/blob/master/src/routes/stats/%2Bpage.svelte#L117).
- Show the user’s [top tracks](https://github.com/jldavis23/my-spotify-stats/blob/master/src/lib/components/TopTracks.svelte) and [artists](https://github.com/jldavis23/my-spotify-stats/blob/master/src/lib/components/TopArtists.svelte) within a user-specified time frame (last 4 weeks, last 4 months, or all time).
- A user can click on a [track](https://github.com/jldavis23/my-spotify-stats/blob/master/src/lib/components/SongModal.svelte) or [artist](https://github.com/jldavis23/my-spotify-stats/blob/master/src/lib/components/ArtistModal.svelte) to view more information (genre, duration, etc.).
- The app will provide [track suggestions](https://github.com/jldavis23/my-spotify-stats/blob/master/src/lib/components/TrackRecs.svelte) based on the user’s top tracks.
- The user can create a playlist from their [top tracks](https://github.com/jldavis23/my-spotify-stats/blob/master/src/lib/components/TopTracks.svelte#L50) and from [suggested tracks](https://github.com/jldavis23/my-spotify-stats/blob/master/src/lib/components/TrackRecs.svelte#LL52).
- Show [artists related to](https://github.com/jldavis23/my-spotify-stats/blob/master/src/lib/components/RelatedArtists.svelte) the user’s favorite artist(s).
- Users will be able to view their [top music genres](https://github.com/jldavis23/my-spotify-stats/blob/master/src/lib/components/Genres.svelte).
- Display the user’s most obscure [tracks](https://github.com/jldavis23/my-spotify-stats/blob/master/src/lib/components/ObscureTracks.svelte) and [artists](https://github.com/jldavis23/my-spotify-stats/blob/master/src/lib/components/ObscureArtists.svelte).
- The user will be able to see what [decade](https://github.com/jldavis23/my-spotify-stats/blob/master/src/lib/components/Decade.svelte) most of their music comes from.
- The user will be able to view an [analysis of their music choices](https://github.com/jldavis23/my-spotify-stats/blob/master/src/lib/components/AudioFeatures.svelte) by averaging the audio features (acousticness, danceability, energy, instrumentalness, and valence) of their top tracks.
- Show the user’s [most](https://github.com/jldavis23/my-spotify-stats/blob/master/src/lib/components/AudioFeatures.svelte#L54) and [least](https://github.com/jldavis23/my-spotify-stats/blob/master/src/lib/components/AudioFeatures.svelte#L77) danceable, energetic, etc. track
- The app will show users how their music tastes have evolved by [comparing](https://github.com/jldavis23/my-spotify-stats/blob/master/src/lib/components/AudioFeatures.svelte#L126) their current listening habits to their all-time listening habits.
- The app will show the user’s [currently playing](https://github.com/jldavis23/my-spotify-stats/blob/master/src/routes/stats/%2Bpage.svelte#L166) track.
- The user can see a list of their [recently played](https://github.com/jldavis23/my-spotify-stats/blob/master/src/lib/components/RecentlyPlayed.svelte) tracks.
- The UI showcases several [animations](https://github.com/jldavis23/my-spotify-stats/blob/master/src/routes/stats/%2Bpage.svelte#L315) when sorting and showing/hiding elements.