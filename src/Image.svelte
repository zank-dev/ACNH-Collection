<script>
    import { onMount } from "svelte";
    
    export let placeholder, src, alt;

    let intersected, loaded = false;
    let imgElement, observer, filePath;

    const observerCallback = function(entries, observer) {
        entries.forEach(entry => {
            if (entry.isIntersecting) {
                intersected = true;
                observer.unobserve(imgElement);
            }
        });
    };

    $: filePath = intersected ? src : placeholder;

    function handleLoad() {
        if (!loaded && filePath === src) {
            loaded = true;
        }
    }
    
    onMount(() => {
        observer = new IntersectionObserver(observerCallback)
        observer.observe(imgElement);
    
        return () => {
            observer.unobserve(imgElement);
        };
    });
</script>

<img src={filePath} alt={alt} on:load={handleLoad} bind:this={imgElement} />