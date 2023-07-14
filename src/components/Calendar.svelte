<script>
    const date = new Date();

    const monthNames = ['January', 'February', 'March', 'April', 'May', 'June', 'July', 'August', 
    'September', 'October', 'November', 'December'];

    const today = {
        day: date.getDate(),
        month: date.getMonth(),
        year: date.getFullYear(),
    }

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
</script>


<main>
    <div class="calendarWrapper">
        <div class="month">
            <ul style="display: flex; justify-content: space-between;">
                <li class="prev" on:click={goToPrevMonth}>&#10094;</li>
                <li>{month}<br>{year}</li>
                <li class="next" on:click={goToNextMonth}>&#10095;</li>          
              </ul>
        </div>
          
        <ul class="weekdays">
            <li>Su</li>
            <li>M</li>
            <li>Tu</li>
            <li>W</li>
            <li>Th</li>
            <li>F</li>
            <li>Sa</li>
        </ul>
        
        <ul class="days">
            {#each Array(cellQuantity) as _, i}  <!-- Can also do: {length: 42}-->
                {#if i < firstDayIndex || i >= numberOfDays+firstDayIndex}
                    <li>&nbsp;</li>
                {:else}
                    <li class:active={i == (currentDay-1)+firstDayIndex && monthIndex==today.month && year==today.year}>{(i+1)-firstDayIndex}</li>
                {/if}
            {/each}
        </ul>
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
    }

    /* Month header */
    .month {
    padding: 70px 25px;
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
    }
    .next{
        cursor: pointer;
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
    margin: 0;
    }

    .days li {
    display: inline-block;
    border: 1px solid black;
    padding: 9px;
    width: 14.28%;
    text-align: center;
    margin-bottom: 1px;
    font-size: 1.2rem;
    color: #777;
    cursor: pointer;
    }

    /* Highlight the "current" day */
    .active {
    padding: 5px;
    background: #1abc9c;
    color: white !important
    }
</style>