<script>
    import { createEventDispatcher } from "svelte";
    import "../app.css";

    export let dateID, dailyLog;
    
    const dispatch = createEventDispatcher();


    // Creating a bind between form values and object
    let log = {
        wakeUp_Hr: dailyLog.wakeUp_Hr,
        wakeUp_Min: dailyLog.wakeUp_Min,
        wakeup_am_pm: dailyLog.wakeup_am_pm,

        workout: dailyLog.workout,
        foods: dailyLog.foods,
        
        sleep_Hr: dailyLog.sleep_Hr,
        sleep_Min: dailyLog.sleep_Min,
        sleep_am_pm: dailyLog.sleep_am_pm
    }


    const resetBtn = () => {
        // Resetting log
        log = {            
            wakeUp_Hr: "",
            wakeUp_Min: "",
            wakeup_am_pm: "",

            workout: "",
            foods: "",
            
            sleep_Hr: "",
            sleep_Min: "",
            sleep_am_pm: ""
        }

    }

    const handleClose = () => {
        dispatch('close');
    }

    const submitLog = (log) => {
        dispatch('submitLog', log);
    }
</script>

<div 
class="border-[5px] border-solid border-blue-600 rounded-md bg-blue-200 text-center w-2/5 px-2 pt-2 pb-3 absolute left-[30%] bottom-[5%] max-h-[90%]">
    <div class="x_btn" on:click={handleClose}>&#10006;</div>   <!-- "X" mark close button -->

    <slot/>

    <form id={dateID} on:submit|preventDefault={() => submitLog(log)}>
        <h3 class="question">When did I wake up?</h3><br>
        <div class="time">
            <input type="number" id="wakeUp_Hr" name="wakeUp_Hr" placeholder="12" min="1" max="12" bind:value={log.wakeUp_Hr} required><br>
            <span class="semi-colon">:</span>
            <input type="number" id="wakeUp_Min" name="wakeUp_Min" placeholder="00" min="0" max="59" bind:value={log.wakeUp_Min} required>

            <div class="ml-2">
                <input type="radio" id="wakeUpAm" name="wakeUpTime" value="am" bind:group={log.wakeup_am_pm}>
                <label for="wakeUpAm">AM</label><br/>

                <input type="radio" id="wakeUpPm" name="wakeUpTime" value="pm" bind:group={log.wakeup_am_pm}>
                <label for="wakeUpPm">PM</label>
            </div>
        </div>

        <h3 class="question">Did I today's workout?</h3><br>
        <div class="yes_no">
            <div>
                <input type="radio" id="yes" name="workout" value="yes" bind:group={log.workout} required>
                <label for="yes">Yes</label>
            </div>
            <div>
                <input type="radio" id="no" name="workout" value="no" bind:group={log.workout} required>
                <label for="no">No</label><br>
            </div>
        </div>

        <h3 class="question">Foods I ate today:</h3><br>
        <textarea id="foods" bind:value={log.foods}></textarea><br><br>

        <h3 class="question">When am I going to sleep?</h3><br>
        <div class="time">
            <input type="number" id="Bedtime_Hr" name="Bedtime_Hr" placeholder="12" min="1" max="12" bind:value={log.sleep_Hr}><br>
            <span class="semi-colon">:</span>
            <input type="number" id="Bedtime_Min" name="Bedtime_Min" placeholder="00" min="0" max="59" bind:value={log.sleep_Min}><br>

            <div class="ml-2">
                <input type="radio" id="amSleep" name="SleepTime" value="am" bind:group={log.sleep_am_pm}>
                <label for="amSleep">AM</label><br/>

                <input type="radio" id="pmSleep" name="SleepTime" value="pm" bind:group={log.sleep_am_pm}>
                <label for="pmSleep">PM</label>
            </div>
        </div>

        <div class="actions">
            <button class="btn btn-red" on:click={handleClose}>Cancel</button>
            <input type="reset" value="Reset" class="btn btn-reset" on:click={resetBtn}>
            <input type="submit" value="Save" class="btn btn-green">
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


    .btn-reset {
      @apply bg-yellow-500 text-white;
    }
    .btn-reset:hover {
      @apply bg-yellow-600 cursor-pointer;
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
        @apply flex justify-evenly -m-6 mb-6;
    }


    #foods{
        @apply resize-y -mt-6 mb-2 border-solid border-[1px] border-black rounded-md max-h-16 px-3;
    }


    .time{
        @apply flex justify-center items-stretch mb-8 -mt-6;
    }
    .time input{
        @apply text-center border-solid border-[1px] border-black rounded-md pl-2 text-lg;
    }


    .semi-colon{
        @apply text-black text-4xl mx-[0.2%];
    }

    .actions{
        @apply flex justify-evenly;
    }
</style>