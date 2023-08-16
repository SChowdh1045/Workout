<script>
    import { createEventDispatcher } from "svelte";
    import "../app.css";

    export let dateID;

    const dispatch = createEventDispatcher();

    const handleClose = () => {
        dispatch('close');
    }

    let tracker = {
        wakeUp_Hr: "",
        wakeUp_Min: "",
        am_pm: "",
        workout: "",
        foods: [],
        sleep_Hr: "",
        sleep_Min: "",
    }
</script>

<div 
class="border-[5px] bg-green-200 border-solid border-green-600 rounded-md text-center w-2/5 px-2 pt-2 pb-3 absolute left-[30%] bottom-[5%] max-h-[90%]">
    <div class="x_btn" on:click={handleClose}>&#10006;</div>   <!-- "X" mark close button -->

    <slot/>

    <form id={dateID}>
        <label for="wakeUp" class="question">When did I wake up?</label><br>
        <div class="time">
            <input type="number" id="wakeUp" name="wakeUp_Hr" placeholder="12" min="1" max="12" bind:value={tracker.wakeUp_Hr}><br>
            <span class="semi-colon">:</span>
            <input type="number" id="wakeUp" name="wakeUp_Min" placeholder="00" min="0" max="59" bind:value={tracker.wakeUp_Min}>

            <div class="ml-2">
                <input type="radio" id="am" name="time" value="am" class="text-lg">
                <label for="am">AM</label><br/>
                <input type="radio" id="pm" name="time" value="pm">
                <label for="pm">PM</label>
            </div>
        </div>

        <h3 class="question">Did I today's workout?</h3><br>
        <div class="yes_no">
            <div>
                <input type="radio" id="yes" name="workout" value="yes">
                <label for="yes">Yes</label>
            </div>
            <div>
                <input type="radio" id="no" name="workout" value="no">
                <label for="no">No</label><br>
            </div>
        </div>

        <h3 class="question">Foods I ate today:</h3><br>
        <textarea class="foods"></textarea><br><br>

        <label for="Bedtime" class="question">When am I filling out this form?</label><br>
        <div class="time">
            <input type="number" id="Bedtime" name="Bedtime_Hr" placeholder="12" min="1" max="12" bind:value={tracker.sleep_Hr}><br>
            <span class="semi-colon">:</span>
            <input type="number" id="Bedtime" name="Bedtime_Min" placeholder="00" min="0" max="59" bind:value={tracker.sleep_Min}><br>

            <div class="ml-2">
                <input type="radio" id="am" name="time" value="am">
                <label for="am">AM</label><br/>
                <input type="radio" id="pm" name="time" value="pm">
                <label for="pm">PM</label>
            </div>
        </div>

        <div class="submit">
            <input type="submit" value="Cancel" class="btn btn-red" on:click={handleClose}>
            <input type="submit" value="Save" class="btn btn-green" on:click={handleClose}>
        </div>
  </form>
</div>


<style>
    .btn {
      @apply font-bold py-2 px-4 rounded mx-5;
    }

    
    .btn-red {
      @apply bg-red-600 text-white;
    }
    .btn-red:hover {
      @apply bg-red-700 cursor-pointer;
    }


    .btn-green {
      @apply bg-green-600 text-white;
    }
    .btn-green:hover {
      @apply bg-green-700 cursor-pointer;
    }


    .x_btn{
        @apply absolute left-[92%] top-[1%] px-1;
    }
    .x_btn:hover{
        @apply border-[1px] border-solid bg-red-600 rounded-md cursor-pointer text-white;
    }


    .question{
        @apply underline font-medium;
    }


    .yes_no{
        @apply flex justify-evenly -m-6 mb-5;
    }


    .foods{
        @apply resize-y -mt-6 border-solid border-[1px] border-black rounded-md max-h-16 px-3;
    }


    .time{
        @apply flex justify-center items-stretch mb-5;
    }
    .time input{
        @apply text-center border-solid border-[1px] border-black rounded-md pl-2 text-lg;
    }


    .semi-colon{
        @apply text-black text-4xl mx-[0.2%];
    }

    .submit{
        @apply flex justify-between;
    }
</style>