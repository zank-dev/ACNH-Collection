<script>
	import { onMount } from 'svelte';
	import Navbar from './Navbar.svelte';
	import Main from './Main.svelte';

	const lightCSS = document.querySelectorAll('.light');
	const darkCSS = document.querySelectorAll('.dark');

	$:colourMode = 'dark';

	function swapMode() {
		if(colourMode === 'dark'){
			setLightMode();
			} else {
			setDarkMode();
		}
		localStorage.setItem('colourMode', colourMode);
	}

	function setDarkMode(){
		colourMode = 'dark';
		darkCSS.forEach(element => {
			element.media = '';
		});
		lightCSS.forEach(element => {
			element.media = 'none';
		});
	}

	function setLightMode(){
		colourMode = 'light';
		darkCSS.forEach(element => {
			element.media = 'none';
		});
		lightCSS.forEach(element => {
			element.media = '';
		});
	}

	onMount(() => {
		colourMode = localStorage.getItem('colourMode') ? localStorage.getItem('colourMode') : 'dark';
		if(colourMode === 'light'){
			setLightMode();
		}
	});
</script>

<main>
	<Navbar on:swapModeEvent={swapMode} {colourMode} />
	<Main {colourMode} />
</main>