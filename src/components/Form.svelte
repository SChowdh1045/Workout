<script>
    import { createEventDispatcher } from "svelte";
    import "../app.css";

    const dispatch = createEventDispatcher();

    const handleClose = () => {
        dispatch('close');
    }
</script>

<!-- <div class="border-[5px] bg-green-200 border-solid border-green-600 rounded-md text-center w-2/5 mx-auto p-2 absolute left-[30%] bottom-[10%]">
    <slot />
    <form action="/action_page.php">
        <label for="wakeUp">When did I wake up?</label><br>
        <input type="text" id="wakeUp" name="wakeUp" value="John"><br>

        <label for="workout">Did I today's workout?</label><br>
        <input type="text" id="workout" name="workout" value="Doe"><br><br>

        <label for="bike">Did I bike outside today?</label><br>
        <input type="text" id="bike" name="bike" value="Doe"><br><br>

        <label for="Foods">Foods I ate today:</label><br>
        <input type="text" id="Foods" name="Foods" value="Doe"><br><br>

        <label for="Bedtime">When am I filling out this form?</label><br>
        <input type="text" id="Bedtime" name="Bedtime" value="Doe"><br><br>

        <!-- <input type="submit" value="Submit"> -->
      <!-- </form> 
    <button class="btn btn-blue" on:click={handleClose}>Close</button>
</div> --> -->


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
</style>