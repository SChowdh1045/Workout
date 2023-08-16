<script>
    import Form from './Form.svelte';

    const date = new Date();

    const monthNames = ['January', 'February', 'March', 'April', 'May', 'June', 'July', 'August', 
    'September', 'October', 'November', 'December'];

    const today = {
        day: date.getDate(),
        month: date.getMonth(),
        year: date.getFullYear(),
    }

    let show_form = false;
    let dayClicked = 0;

    let monthIndex = date.getMonth();

    let currentDay = date.getDate();
    $: month = monthNames[monthIndex];
    let year = date.getFullYear();

    $: firstDayIndex = new Date(year, monthIndex, 1).getDay();  // .getDay() goes from 0-6 (Sun-Sat respectively)
    $: numberOfDays = new Date(year, monthIndex+1, 0).getDate();  // [ the 0 in "Date()" means the LAST day of the PREVIOUS month ; 
                                                                    // getDate() returns the day of the month (from 1-31) ;
                                                                    // Adding +1 because I need current month (So if right now is July, I need to add +1 to the monthIndex to August to indicate the last day of July (total # of days in July)) ]

    // To calculate how many cells there should be in the calendar for the given month (to avoid extra unnecessary rows of cells)
    $: cellQuantity = Math.ceil((firstDayIndex + numberOfDays) / 7) * 7;  // firstDayIndex is to know how many blank spaces there are before the first day
                                                                            //  Add firstDayIndex + numberOfDays to figure out how far to push the calendar down
                                                                            // Then divide by 7 to know how many rows there would be. Take the ceiling of that number then multiply by 7 to have nice calendar format

    
    
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


    let grayedOut = false;
    let highlight = true;
    let dateID;

    const showFormFunction = (e, day_number) => {
        show_form = true;
        dayClicked = day_number;
        grayedOut = true;
        highlight = false;
        dateID = e.target.dataset.dateid;
    }

    const handleClose = () => {
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

            <button id="today_btn" on:click={goToToday}>TODAY</button>
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
                    <li><div class="dot_blank"></div>&nbsp;</li>
                {:else}
                    <!-- "(currentDay-1)+firstDayIndex"  targets the original numbering. For this line, it's to see if the iterable "i" value is the current day's value.
                    On the other hand, "(i+1)-firstDayIndex" is to modify the numbering from the original. This is to make it to an actual calendar. 
                    NOTE: When dealing with the iterable "i", we are working with the original numbering under the hood despite it being presented as modified for the user. -->
                    <li class:highlight class:active={i == (currentDay-1)+firstDayIndex && monthIndex==today.month && year==today.year}  data-dateID="{month}_{(i+1)-firstDayIndex}_{year}" on:click={highlight ? (e) => showFormFunction(e, (i+1)-firstDayIndex) : null}>
                        <div class="dot"></div>{(i+1)-firstDayIndex}
                    </li> 
                {/if}
            {/each}
        </ul>
        

        {#if show_form}
            <Form {dateID} on:close={handleClose}>
                <h1 style="margin-bottom: 8%; font-size: x-large; font-weight: 600;">Summary for <br/> {month} {dayClicked}, {year}</h1>
            </Form>
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

    #today_btn{
        border: 3px solid white;
        border-radius: 5px;
        padding: 0.90%;
        margin-top: 5%;
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
    padding: 5px;
    background: #1abc9c;
    color: white !important;
    }

    .dot {
    height: 12px;
    width: 12px;
    background-color: #ff0000;
    border-radius: 50%;
    margin: auto;
    }

    .dot_blank{
    height: 12px;
    width: 12px;
    background-color: #eee;
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