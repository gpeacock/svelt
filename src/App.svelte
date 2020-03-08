<script>
	import { onMount } from 'svelte';

	function authorizationRedirect() {
		// redirect to adobe authorization and then send back here
		var apiKey = "6926781233444501ad68416e692fa8e9"
		var authEndpoint = "https://ims-na1.adobelogin.com/ims/authorize/v1";
		var redirectUrl = window.location.href; 
		var scope = "openid,creative_sdk";
		var requestEndpoint =
			authEndpoint +
			"?response_type=token" +
			"&client_id=" + encodeURIComponent(apiKey) +
			"&redirect_uri=" + encodeURIComponent(redirectUrl) +
			"&scope=" + encodeURIComponent(scope);
		window.location.href = requestEndpoint;
	}

	function getAuthorization() {
		// if we are returning from a redirect auth, then read the authorization from hash
		let accessToken  = null
		if (window.location.hostname == "localhost") {
			return accessToken  // embed an access token here for local debugging

		}
		var hash = window.location.hash.replace("#", "");
		if (hash !== "") {
			var redirectProperties = hash.split("&");
			for (var i = 0; i < redirectProperties.length; i++) {
			if (redirectProperties[i].indexOf("access_token=") === 0) {
				accessToken = redirectProperties[i].split("=")[1];
				// remove the hash token from the browser display
				history.replaceState({}, document.title, window.location.href.split('#')[0]);
				break;
			}
			}
		}
		if (accessToken == null) {
			authorizationRedirect()
		} else {
			return accessToken 
	}
}

	let photos = [];

	onMount(async () => {
		getAuthorization()
		const res = await fetch(`https://jsonplaceholder.typicode.com/photos?_limit=30`);
		photos = await res.json();
	});
	
	function doChange() {
		photos.map((photo,index)=> { photo, index 
		photos[index].title = "foo"+index
		photos[index].thumbnailUrl = "https://loremflickr.com/255/255?random="+Math.floor(Math.random() * 1000)
		 })
	}
</script>

<style>
	.photos {
		width: 100%;
		display: grid;
		grid-template-columns: repeat(5, 1fr);
		grid-gap: 8px;
	}

	figure, img {
		width: 100%;
		margin: 0;
	}
</style>

<h1>Photo album</h1>
<button on:click={doChange}>
	change
</button>
<div class="photos">
	{#each photos as photo}
		<figure>
			<img src={photo.thumbnailUrl} alt={photo.title}>
			<figcaption>{photo.title}</figcaption>
		</figure>
	{:else}
		<!-- this block renders when photos.length === 0 -->
		<p>loading...</p>
	{/each}
</div>