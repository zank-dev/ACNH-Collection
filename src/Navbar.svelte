<script>
	import { onMount, createEventDispatcher } from 'svelte';

	const dispatch = createEventDispatcher();
	
	export let colourMode;
	let time = new Date();
	let navbar;

	$:hours = ('0' + time.getHours()).slice(-2);
	$:minutes = ('0' +time.getMinutes()).slice(-2);
	$:seconds = ('0' + time.getSeconds()).slice(-2);

	function swapModeEvent() {
		dispatch('swapModeEvent');
	}

	onMount(() => {
		const interval = setInterval(() => {
			time = new Date();
		}, 1000);
		navbar = document.getElementById('navbar');

		return () => {
			clearInterval(interval);
		};
	});
</script>

<div id="navbar" class="hero-sm hero-body" class:bg-dark={ colourMode === 'dark' } class:bg-gray={ colourMode === 'light' } >
  <header class="navbar container grid-lg">
	<section class="navbar-section">
	  <span class="text-primary">
		<i class="icon icon-2x icon-time" />
		{hours}:{minutes}:{seconds}
	  </span>
	</section>
	<section class="navbar-center">
	  <img src="/img/icons/icon.svg" alt="Icon" />
	  <span class="text-primary">ACNH - Collection</span>
	</section>
	<section class="navbar-section">
	  <a href="https://twitter.com/Drachenbrut4" class="btn btn-link" target="_blank">Twitter</a>
	  <a href="https://github.com/ZankHunt/ACNH-Collection" class="btn btn-link" target="_blank">GitHub</a>
	  <label class="form-switch">
		<input type="checkbox" label="swapColor" on:click={swapModeEvent} checked={ colourMode === 'light' } />
		<i class="form-icon" />
	  </label>
	</section>
  </header>
</div>

<style>
  div {
	width: 100vw;
	position: fixed;
	top: 0;
	z-index: 3;
  }

  .navbar-center span {
	font-weight: bold;
  }

  img {
	width: 2rem;
	height: auto;
	padding: 0.25rem 0.4rem;
  }
</style>