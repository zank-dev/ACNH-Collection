<script>
	import { onMount } from 'svelte';
	import CollectionTable from './CollectionTable.svelte';
	import Settings from './Settings.svelte';
	import Search from './Search.svelte';

	export let colourMode;
	let search = '';
	let loading = true;
	let caughtMode = 'uncaught';

	function searchEvent (event) {
		search = event.detail;
	}

	function swapedCaughtMode (event) {
		caughtMode = event.detail;
	}

	onMount(() => {
		setTimeout(() => {
			loading = false;
		}, 1000);
	});
</script>

<div id="main" class="hero hero-body" class:bg-dark={ colourMode === 'light' } class:bg-gray={ colourMode === 'dark' }>
	<main class="container grid-lg">
		<h1>ACNH - Collection</h1>
		<p>This site helps you to track and finish your collection!</p>
		<Settings on:searchEvent={searchEvent} on:swapedCaughtMode={swapedCaughtMode} />
		<Search {colourMode} on:searchEvent={searchEvent} />
		{#if loading === true}
			<div class="loading loading-lg"></div>
		{:else}
		<CollectionTable {colourMode} {search} {caughtMode} />
		{/if}
	</main>
</div>
<div id="footer" class="hero-sm hero-body" class:bg-gray={ colourMode === 'light' }>
	<footer class="container grid-lg">
		Created by <a href="https://twitter.com/Drachenbrut4" target="_blank">Zank</a>
	</footer>
</div>

<style>
	h1 {
		font-weight: bold;
		text-align: center;
	}

	p {
		text-align: center;
	}

	footer {
		text-align: center;
		margin: 0 auto;
	}
</style>