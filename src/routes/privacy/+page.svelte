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
	<h1 class="text-4xl">Privacy Policy</h1>

    <div class="flex flex-col gap-5 my-5">
        <p>
            All information displayed by this app comes directly from the Spotify Web API. If you choose to
            use this app, you agree to let the app take actions in Spotify on your behalf (creating and
            editing playlists) and to use your Spotify account data and your activity.
        </p>

        <p>
            The information you provide is not stored or collected anywhere. It is used for the sole purpose
            of displaying information about your Spotify listening habits.
        </p>

        <p>We do not use any Cookies.</p>

        <p>
            You can revoke My Spotify Stats' permissions any time by going to your <a
                href="https://www.spotify.com/us/account/apps/" class="link link-primary">Manage Apps page</a
            > on Spotify and clicking "Remove Access" next to My Spotify Stats
        </p>
    </div>
</div>
