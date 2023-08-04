<script>
    import { createEventDispatcher } from "svelte";
    import "../app.css";

    const dispatch = createEventDispatcher();

    const handleClose = () => {
        dispatch('close');
    }
</script>

<div class="border-8 border-solid border-green-600 rounded-md text-center w-2/5 mx-auto p-2">
    <slot />
    <button class="btn btn-blue" on:click={handleClose}>Close</button>
</div>


<style>
    .btn {
      @apply font-bold py-2 px-4 rounded;
    }
    .btn-blue {
      @apply bg-red-600 text-white;
    }
    .btn-blue:hover {
      @apply bg-red-700 cursor-pointer;
    }

    button{
      transition: 0.1s;
    }
</style>