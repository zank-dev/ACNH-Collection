<script>
    import { createEventDispatcher, onMount } from 'svelte';

    const dispatch = createEventDispatcher();

    export let colourMode;
    let searchValue = '';

    function sendSearch(){
        dispatch('searchEvent', searchValue);
    }

    function resetInput(){
        dispatch('searchEvent', '');
        searchValue = '';
    }

    function handleKeydown(event)
    {
        if(event.key === 'Enter') {
            resetInput();
        }
    }
</script>

<div class="has-icon-left input-group">
    <input on:keydown={handleKeydown} on:input={sendSearch} bind:value={searchValue} type="text" class="form-input" placeholder="Search">
    <i class="form-icon icon icon-search" class:text-dark={ colourMode === 'light' }></i>
    <button on:click={resetInput} class="btn btn-primary input-group-btn">Reset</button>
</div>

<style>
    div {
        padding: 0.5rem 0;
    }

    button {
        width: 5rem;
    }
</style>