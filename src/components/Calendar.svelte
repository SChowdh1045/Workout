<script>
    import Form from './Form.svelte';
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
    let goDay, goMonth, goYear = 0;

    // Have to make 2 different objects to track daily logs. "logTracker" for component render & "localStorage" for persistent data (after I close session and re-open application, data will not be wiped).
    // What I mean by " "logTracker" for component render ", is that when I did it with localStorage, when I saved a log, the workout color dot would not appear right away. I'm guessing it's because localStorage doesn't do a re-render?
    // It's only after restarting the application that the dot would appear, which was not ideal. Therefore, logTracker object first takes in all the existing data from localStorage, then have to parse the values, since in localStorage, they're stored as strings. Then assign it back to logTracker.
    // After that, the daily logs would get added to both logTracker & localStorage the normal way (inside "saveSchdeule" function).
    // NOTE: "Object.entries(localStorage)" returns an array of array/s. So, returns one (big?) array where each of its elements are arrays themselves containing property-value pairs of the passed object.
    let logTracker = {...localStorage};
    for (const [key, value] of Object.entries(localStorage)){
        logTracker[key] = JSON.parse(value);
    }

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

    let goToDate = "";
    const goToDateSplitter = () => {
        let splitDate = goToDate.split("-");  // The value of the "date" input returns as "yyyy-mm-dd" format. I just want year & month, so I have to split by "-" then take the values from the returned list.

        goYear = year = parseInt(splitDate[0]);
        goMonth = monthIndex = parseInt(splitDate[1]) - 1; // Since monthIndex takes values from 0-11 (Jan - Dec respectively) and the splitDate values go from 1-12, I need to subtract 1 to get the right index number
        goDay = parseInt(splitDate[2]);

        // I put this line because I wanted to fix a bug where after the selected date is shown and I wanted go to another month/year, then come back to the selected date's month/year again, the selected animation (ping) would happen again, and I didn't want that.
        // Because the animation lasts for 2700ms (0.9s * 3), I decided to set a setTimeout function where the "goDay" value changes after 3 seconds, thereby stopping the animation from happening after 3 seconds.
        setTimeout(() => {goDay = 0;}, 3000);
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

        // Adding in a new day log to logTracker & localStorage (see explanation in line 43).
        logTracker = Object.defineProperty(logTracker, dateID, {value: e.detail, writable: true});
        localStorage.setItem(dateID, JSON.stringify(e.detail));

        
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
                <li class:prev_next={highlight} on:click={highlight ? goToPrevMonth : null}>&#10094;</li>
                <li style="color: #fff; font-weight: bold;">{month}<br>{year}</li>
                <li class:prev_next={highlight} on:click={highlight ? goToNextMonth : null}>&#10095;</li>          
            </ul>

            <hr style="margin: 4% 0;"/>

            <h2 style="color: #fff;">Select A Date:</h2>
            <div style="display: flex; justify-content: space-around; align-items: center;">

                <button class="date_btn" on:click={highlight ? goToToday : null}>TODAY</button>                
                
                <form style="display: flex; justify-content: space-around;" on:submit|preventDefault={goToDateSplitter}>
                    <input type="date" id="date_selection" name="date_selection" bind:value={goToDate} required>
                    <input type="submit" class="date_btn" value="Go">
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
                    <li class:highlight={highlight && (new Date(`${month} ${(i+1)-firstDayIndex}, ${year}`).getTime() <= date.getTime())}  class:active={i == ((today.day-1)+firstDayIndex) && (monthIndex==today.month) && (year==today.year)}  class:inaccessible={date.getTime() < new Date(`${month} ${(i+1)-firstDayIndex}, ${year}`).getTime()}  class:go={goDay == (i+1)-firstDayIndex && goMonth == monthIndex && goYear == year}  data-dateID="{month}_{(i+1)-firstDayIndex}_{year}" on:click={highlight && (new Date(`${month} ${(i+1)-firstDayIndex}, ${year}`).getTime() <= date.getTime()) ? (e) => showFormFunction(e, (i+1)-firstDayIndex) : null}>
                        <div class="hidden_dot"
                        class:green_dot={`${month}_${(i+1)-firstDayIndex}_${year}` in logTracker && logTracker[`${month}_${(i+1)-firstDayIndex}_${year}`].workout == 'yes'}
                        class:red_dot={`${month}_${(i+1)-firstDayIndex}_${year}` in logTracker && logTracker[`${month}_${(i+1)-firstDayIndex}_${year}`].workout == 'no'}>
                        </div>
                        {(i+1)-firstDayIndex}
                    </li> 
                {/if}
            {/each}
        </ul>
        
        <!-- The selected form will be presented with either existing values (meaning I've entered values before for this date) 
            or with default values (meaning this is my first time opening this date.) -->
        {#if show_form}
            <Form {dateID} dailyLog={dateID in logTracker ? logTracker[dateID] : defaultLog} on:close={handleClose} on:submitLog={saveSchdeule}>
                <h1 style="margin-bottom: 8%; font-size: x-large; font-weight: 600;">Summary for <br/> {month} {dayClicked}, {year}</h1>
            </Form>
        {/if}
    </div> 
</main>


<style>
    ul {@apply list-none;}

    main {
        @apply font-['Verdana'] my-[5%] mx-0;
    }

    

    .calendarWrapper{
        @apply w-4/5 mx-auto border-4 border-solid border-blue-600 rounded relative;
    }

    .grayedOut{
        @apply bg-black opacity-50;
    }


    /* Month header */
    .month {
        @apply pt-[70px] px-6 pb-6 bg-blue-400 text-center;
    }

    .month ul li {
        @apply text-white text-xl uppercase tracking-widest;
    }

    .prev_next{
        @apply duration-100;
    }

    .prev_next:hover{
        @apply cursor-pointer border-2 border-solid border-white rounded-lg p-[0.1%];
    }

    .date_btn{
        @apply border-[3px] border-solid border-white rounded-md p-[0.9%] text-white font-bold duration-200 bg-blue-600;
    }

    .date_btn:hover{
        @apply cursor-pointer p-[1.1%] bg-blue-500;
    }

    #date_selection{
        @apply border border-solid border-white mx-[2%];
    }

    .go{
        animation: pulse 0.9s cubic-bezier(0.4, 0, 0.6, 1) 3;
    }

    @keyframes pulse {
        0% {background-color: #f8fafb; }
        50% {background-color: #7db7ff; }
        100% {background-color: #f8fafb; }
    }


    /* Weekdays (Mon-Sun) */
    .weekdays {
        @apply flex justify-around m-0 py-[10px] px-0 bg-slate-200;
    }

    .weekdays li {
        @apply text-slate-700;
    }

    /* Days (1-31) */
    .days {
        @apply bg-slate-50;
    }

    /* Width was calculated by taking the month's div's width subtracted by its (padding * 2). Then take the difference and divide by 7 to get the pixel width size of each day li tag.
    To get it in percentage form, take the quotient and divide it by the number you get after you subtracted the month's width by its (padding * 2) */
    .days li {
        @apply inline-block border-[1px] border-solid border-black p-[9px] w-[14.28%] text-center text-lg text-slate-800;
    }

    .highlight:hover{
        @apply cursor-pointer bg-zinc-200;
    }

    /* Highlight the "current" day */
    .active {
        background: #60a5fa;
        color: white !important;
        font-weight: bold;
    }

    .active:hover{
        @apply bg-[#7fb4f5] font-bold;
    }

    .inaccessible{
        @apply cursor-not-allowed;
    }

    .hidden_dot{
        @apply h-3 w-3;
    }

    .green_dot{
        @apply h-3 w-3 bg-green-700 rounded-full m-auto;
    }

    .red_dot{
        @apply h-3 w-3 bg-red-600 rounded-full m-auto;
    }
</style>