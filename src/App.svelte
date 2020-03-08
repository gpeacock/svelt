<script>
	import { onMount } from 'svelte';

	let photos = [];

	onMount(async () => {
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