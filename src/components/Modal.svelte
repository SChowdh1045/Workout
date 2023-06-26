<script>
    import { createEventDispatcher } from "svelte";
    const dispatch = createEventDispatcher();

    const handleClose = () => {
        dispatch('close');
    }
</script>

<div style="border: 2px solid red;">
    Something
    <slot></slot>
    <button on:click={handleClose}>close</button>
</div>