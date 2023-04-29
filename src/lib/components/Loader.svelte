<!-- source: https://blog.hubspot.com/website/css-loading-animation -->

<div class="loader border-8 border-primary" />

<style>
	.loader {
		/* margin: auto; */
		/* border: 8px solid #eaf0f6; */
		border-radius: 50%;
		border-top: 8px solid hsl(var(--n) / var(--tw-border-opacity)); /* border-neutral */
		width: 50px;
		height: 50px;
		animation: spinner 0.75s linear infinite;
	}

	@keyframes spinner {
		0% {
			transform: rotate(0deg);
		}
		100% {
			transform: rotate(360deg);
		}
	}
</style>
