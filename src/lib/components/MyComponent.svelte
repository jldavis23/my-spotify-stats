<script>
    import { onMount } from 'svelte'

	export let accessToken;

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

    onMount(async () => {
        const profile = await fetchProfile(accessToken)
        console.log(profile)
	});

    
</script>

<main>
	<p>hey there</p>
</main>
