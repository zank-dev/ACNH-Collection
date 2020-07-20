<script>
    import { createEventDispatcher } from 'svelte';

    const dispatch = createEventDispatcher();

    function selectCategory(term) {
        if(term === ''){
            document.getElementById('all-button').classList.add('active');
            document.getElementById('bugs-button').classList.remove('active');
            document.getElementById('fishs-button').classList.remove('active');
            document.getElementById('deep-sea-button').classList.remove('active');
        } else if(term === 'bug') {
            document.getElementById('all-button').classList.remove('active');
            document.getElementById('bugs-button').classList.add('active');
            document.getElementById('fishs-button').classList.remove('active');
            document.getElementById('deep-sea-button').classList.remove('active');
        } else if(term === 'fish') {
            document.getElementById('all-button').classList.remove('active');
            document.getElementById('bugs-button').classList.remove('active');
            document.getElementById('fishs-button').classList.add('active');
            document.getElementById('deep-sea-button').classList.remove('active');
        } else {
            document.getElementById('all-button').classList.remove('active');
            document.getElementById('bugs-button').classList.remove('active');
            document.getElementById('fishs-button').classList.remove('active');
            document.getElementById('deep-sea-button').classList.add('active');
        }
        dispatch('searchEvent', term);
    }

    function swapedCaughtMode(mode) {
        if(mode === 'uncaught'){
            document.getElementById('uncaught-button').classList.add('active');
            document.getElementById('caught-button').classList.remove('active');
        } else {
            document.getElementById('caught-button').classList.add('active');
            document.getElementById('uncaught-button').classList.remove('active');
        }
        dispatch('swapedCaughtMode', mode);
    }
</script>

<section>
    <h2>Settings:</h2>
    <div class="btn-group btn-group-block">
        <button id="uncaught-button" class="btn active" on:click={() => swapedCaughtMode('uncaught')}>Uncaught</button>
        <button id="caught-button" class="btn" on:click={() => swapedCaughtMode('caught')}>Caught</button>
    </div> 
    <div class="btn-group btn-group-block">
        <button id="all-button" class="btn active" on:click={() => {selectCategory('')}}>All</button>
        <button id="bugs-button" class="btn" on:click={() => {selectCategory('bug')}}>Bugs</button>
        <button id="fishs-button" class="btn" on:click={() => {selectCategory('fish')}}>Fishs</button>
        <button id="deep-sea-button" class="btn" on:click={() => {selectCategory('underwater')}}>Deep-sea Creatures</button>
    </div>
</section>

<style>
    h2 {
		text-align: left;
    }
    
    section {
        padding: 0.5rem 0;
    }
</style>