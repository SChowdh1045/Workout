<script>
    import Form from './Form.svelte';
    import {test} from '../store';
    import { onDestroy } from 'svelte';
    import "../app.css";


    const date = new Date();

    const monthNames = ['January', 'February', 'March', 'April', 'May', 'June', 'July', 'August', 
    'September', 'October', 'November', 'December'];

    const today = {
        day: date.getDate(),
        month: date.getMonth(),
        year: date.getFullYear(),
    }

    let dayClicked = 0;

    let monthIndex = date.getMonth();
    $: month = monthNames[monthIndex];

    let year = date.getFullYear();

    $: firstDayIndex = new Date(year, monthIndex, 1).getDay();  // .getDay() goes from 0-6 (Sun-Sat respectively)
    $: numberOfDays = new Date(year, monthIndex+1, 0).getDate();  // [ the 0 in "Date()" means the LAST day of the PREVIOUS month ; 
                                                                    // getDate() returns the day of the month (from 1-28/29/30/31) ;
                                                                    // Adding +1 because I need current month (So if right now is July, I need to add +1 to the monthIndex to August to indicate the last day of July (total # of days in July)) ]

    // To calculate how many cells there should be in the calendar for the given month (to avoid extra unnecessary rows of cells)
    $: cellQuantity = Math.ceil((firstDayIndex + numberOfDays) / 7) * 7;  // firstDayIndex is to know how many blank spaces there are before the first day
                                                                            //  Add firstDayIndex + numberOfDays to figure out how far to push the calendar down
                                                                            // Then divide by 7 to know how many rows there would be. Take the ceiling of that number then multiply by 7 to have nice calendar format

    
    let show_form = false;
    let grayedOut = false;
    let highlight = true;
    let dateID;
    let logTracker = {};
    
    let defaultLog = {
        wakeUp_Hr: "",
        wakeUp_Min: "",
        wakeup_am_pm: "",

        workout: "",
        foods: "",
        
        sleep_Hr: "",
        sleep_Min: "",
        sleep_am_pm: ""
    }
    


    // *** FUNCTIONS ***  //
    const goToPrevMonth = () => {
        monthIndex -= 1;

        if (monthIndex < 0){
            monthIndex = 11;
            year -= 1;
        }
    }

    const goToNextMonth = () => {
        monthIndex += 1;

        if (monthIndex > 11){
            monthIndex = 0;
            year += 1;
        }
    }

    const goToToday = () => {
        monthIndex = today.month;  // or can do just "monthIndex = date.getMonth()"
        year = today.year;  // or can do just "year = date.getFullYear()"
    }


    const showFormFunction = (e, day_number) => {
        dateID = e.target.dataset.dateid;  // Now both <li> tag and form have an ID (the same ID)
        show_form = true;
        dayClicked = day_number;
        grayedOut = true;
        highlight = false;
    }

    const handleClose = () => {
        show_form = false;
        grayedOut = false;
        highlight = true;
    }

    const saveSchdeule = (e) => {
        let wakeUp_Min = e.detail.wakeUp_Min;
        let sleep_Min = e.detail.sleep_Min;
        // The following 2 lines are for the minutes input field. If the user enters a value less than 10, there should be a 0 before it.
        // And if the first value is NOT already a 0, add a 0 before it. (This is to fix the bug where after saving and opening again, an extra 0 gets added everytime)
        (wakeUp_Min < 10) && (wakeUp_Min[0] !== '0') && (wakeUp_Min !== "") ? (e.detail.wakeUp_Min = '0' + wakeUp_Min) : null;
        (sleep_Min < 10) && (sleep_Min[0] !== '0') && (sleep_Min !== "")? (e.detail.sleep_Min = '0' + sleep_Min) : null;

        // Adding in a new day log to logTracker object. The object that was passed to the function, with the specified property added or modified.
        logTracker = Object.defineProperty(logTracker, dateID, {value: e.detail, writable: true});
        
        // Copied from "handleClose"
        show_form = false;
        grayedOut = false;
        highlight = true;
    }
</script>


<main>
    <div class="calendarWrapper">
        <div class="month" class:grayedOut>  <!-- "class:grayedOut" is equivalent to "class:grayedOut={grayedOut}". The first "grayedOut" is the class to toggle. The second "grayedOut" is the boolean variable -->
            <ul style="display: flex; justify-content: space-between; align-items: center;">
                <li class="prev" on:click={goToPrevMonth}>&#10094;</li>
                <li>{month}<br>{year}</li>
                <li class="next" on:click={goToNextMonth}>&#10095;</li>          
            </ul>

            <h2 style="margin-top: 5%;">Jump To:</h2>
            <div class="jumpToWrapper">                
                <button id="today_btn" on:click={goToToday}>TODAY</button>                
                <form>
                    <label for="date_selection">Select A Date:</label>
                    <input type="date" id="date_selection" name="date_selection">
                    <input type="submit" value="Go">
                </form>
            </div>
        </div>
        
        <ul class="weekdays" class:grayedOut>
            <li>Su</li>
            <li>M</li>
            <li>Tu</li>
            <li>W</li>
            <li>Th</li>
            <li>F</li>
            <li>Sa</li>
        </ul>
        
        <ul class="days" class:grayedOut>
            {#each Array(cellQuantity) as _, i}  <!-- Can also do: {length: 42}-->
                {#if i < firstDayIndex || i >= numberOfDays+firstDayIndex}
                    <li><div class="hidden_dot"></div>&nbsp;</li>

                {:else}
                    <!-- "(today.day-1)+firstDayIndex" targets the original numbering. For this line, it's to see if the iterable "i" value is the current day's value.
                    On the other hand, "(i+1)-firstDayIndex" is to modify the numbering from the original. This is to make it to an actual calendar. 
                    NOTE: When dealing with the iterable "i", we are working with the original numbering under the hood despite it being presented as modified for the user.
                    The "highlight" class was made to only highlight the days before and including today, not the days after today. Had to add the highlight boolean variable (on RHS) because without it, when I opened a form and I was hovering over the days preceding today (& today), it would still have the highlight effect in the grayed out background.
                    The "inaccessible" class was made to block the user from opening any days after today (cuz it doesnt make sense to know all the log/form info ahead of time). Simply compares milliseconds of today and the current iterable "i"'s day. 
                    For the "on:click" and all it jargon, it was made so that the days after today cannot be opened. Had to add the highlight boolean variable (on RHS) because without it, when I opened a form and tried to click on any day preceding today (& today), it would open that clicked day's form, which is not what I want.   -->
                    <li class:highlight={highlight && (new Date(`${month} ${(i+1)-firstDayIndex}, ${year}`).getTime() <= date.getTime())}  class:active={i == ((today.day-1)+firstDayIndex) && (monthIndex==today.month) && (year==today.year)}  class:inaccessible={date.getTime() < new Date(`${month} ${(i+1)-firstDayIndex}, ${year}`).getTime()}  data-dateID="{month}_{(i+1)-firstDayIndex}_{year}" on:click={highlight && (new Date(`${month} ${(i+1)-firstDayIndex}, ${year}`).getTime() <= date.getTime()) ? (e) => showFormFunction(e, (i+1)-firstDayIndex) : null}>
                        <div class="hidden_dot"
                        class:green_dot={`${month}_${(i+1)-firstDayIndex}_${year}` in logTracker && logTracker[`${month}_${(i+1)-firstDayIndex}_${year}`].workout === 'yes'}
                        class:red_dot={`${month}_${(i+1)-firstDayIndex}_${year}` in logTracker && logTracker[`${month}_${(i+1)-firstDayIndex}_${year}`].workout === 'no'}>
                        </div>
                        {(i+1)-firstDayIndex}
                    </li> 
                {/if}
            {/each}
        </ul>
        
        <!-- The selected form will be presented with either existing values (meaning I've entered values before for this date) 
            or with default values (meaning this is either my first time opening this date or I reset it and saved.) -->
        {#if (show_form)}
            {#if dateID in logTracker}
                <Form {dateID} dailyLog={logTracker[dateID]} on:close={handleClose} on:submitLog={saveSchdeule}>
                    <h1 style="margin-bottom: 8%; font-size: x-large; font-weight: 600;">Summary for <br/> {month} {dayClicked}, {year}</h1>
                </Form>

            {:else}
                <Form {dateID} dailyLog={defaultLog} on:close={handleClose} on:submitLog={saveSchdeule}>
                    <h1 style="margin-bottom: 8%; font-size: x-large; font-weight: 600;">Summary for <br/> {month} {dayClicked}, {year}</h1>
                </Form>
            {/if}
            
        {/if}
    </div> 
</main>


<style>
    ul {list-style-type: none;}
    main {font-family: Verdana, sans-serif; margin: 5% 0;}

    .calendarWrapper{
        width: 80%;
        margin: auto;
        border: 4px solid rgb(8, 148, 106);
        border-radius: 4px;
        position: relative;
    }

    .grayedOut{
        background-color: black;
        opacity: 0.5;
    }

    /* Month header */
    .month {
    padding: 70px 25px 25px;
    background: #1abc9c;
    text-align: center;
    }

    .month ul li {
    color: white;
    font-size: 20px;
    text-transform: uppercase;
    letter-spacing: 3px;
    }

    .prev{
        cursor: pointer;
        transition: 0.1s;
    }

    .prev:hover{
        border: 2px solid white;
        border-radius: 8px;
        padding: 0.1%;
    }

    .next{
        cursor: pointer;
        transition: 0.1s;
    }

    .next:hover{
        border: 2px solid white;
        border-radius: 8px;
        padding: 0.1%;
    }

    .jumpToWrapper{
        display: flex;
        justify-content: space-around;
        align-items: center;
    }

    #today_btn{
        border: 3px solid white;
        border-radius: 5px;
        padding: 0.90%;
        color: rgb(241, 240, 240);
        font-weight: bold;
        transition: 0.2s;
        background: #349b86;
    }

    #today_btn:hover{
        padding: 1.1%;
        background: #31b49a;
    }

    /* Weekdays (Mon-Sun) */
    .weekdays {
    display: flex;
    justify-content: space-around;
    margin: 0;
    padding: 10px 0;
    background-color:#ddd;
    }

    .weekdays li {
    color: #666;
    text-align: center;
    }

    /* Days (1-31) */
    .days {
    padding: 10px 0;
    background: #eee;
    padding: 0;
    }

    .days li {
    display: inline-block;
    border: 1px solid black;
    padding: 9px;
    width: 14.28%;
    margin: 0;
    text-align: center;
    font-size: 1.2rem;
    color: #777;
    }

    .highlight:hover{
        cursor: pointer;
        background-color: #dfd8d8;
    }

    /* Highlight the "current" day */
    .active {
    background: rgb(13, 161, 75);
    color: white !important;
    font-weight: bold;
    }

    .active:hover{
        background: #1abc9c;
        font-weight: bold;
    }

    .inaccessible{
        cursor: not-allowed;
    }

    .hidden_dot{
    height: 12px;
    width: 12px;
    margin: auto;
    }

    .green_dot{
    height: 12px;
    width: 12px;
    background-color: #2edf69;
    border-radius: 50%;
    margin: auto;
    }

    .red_dot{
    height: 12px;
    width: 12px;
    background-color: #db2424;
    border-radius: 50%;
    margin: auto;
    }

    .close_btn{
        font-weight: bold;
        padding: 0.5rem 1rem;
        border-radius: 5px;
        background-color: rgb(207, 22, 22);
        color: white;
    }

    .close_btn:hover{
        background-color: rgb(226, 48, 48);
        cursor: pointer;
    }
</style>