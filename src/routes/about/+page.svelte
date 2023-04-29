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
				<ul tabindex="0" class="dropdown-content menu p-2 shadow bg-primary rounded-box w-52" />
			</div>
			<p class="normal-case text-xl">My Spotify Stats</p>
		</div>

        <div class="flex-none">
            <a href="/stats" class="btn btn-primary">Login with Spotify</a>
        </div>
	</div>
</div>

<div class="p-10 h-screen">
	<h1 class="text-4xl">About My Spotify Stats</h1>

    <div class="flex flex-col gap-5 my-5">
        <p>
            Spotify Wrapped comes around only once a year, so Jacey Davis (the creator of My Spotify Stats) decided to put together an app that could let you view your Spotify stats at any time of the year. You can view your top tracks, artists, get recommendations, and more.
        </p>

        <p>
            My Spotify Stats was built with SvelteKit and uses data from Spotify's Web API.
        </p>

        
    </div>
</div>
