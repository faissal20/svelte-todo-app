<script>
    import moment from 'moment';
    import DateButton from './sidebarComponents/dateButton.svelte';
    import { createEventDispatcher } from "svelte";
    const dispatch = createEventDispatcher(); 

    export let start;
        
    let day1 = moment(start).format('YYYY/MM/DD');
    let day2 = moment(start).add(1, 'days').format('YYYY/MM/DD');
    let day3 = moment(start).add(2, 'days').format('YYYY/MM/DD');
    
    let dates = [
        {id : 0, day: day1, active: true},
        {id : 1, day: day2, active: false},
        {id : 2, day: day3, active: false}
    ]
    

    function changeStatus(event){
        let newDates = dates.map( item => {
            if(item.day == event.detail.day){
                item.active = true;
                dispatch('message', { date : item.day } );
            }else{
                item.active = false;
            }
            return item;
        });  
        dates = newDates;      
    }

</script>

<div class="sidebar">

    {#each dates as item }

        <div class={ item.active ? "date active" : "date"} >
            <DateButton {...item} on:message={changeStatus}/> 
        </div>
    
    {/each}
    <!-- <div>
        calender
    </div> -->
</div>

<style>
    .sidebar{
        margin: 1rem;
        display: flex;
        flex-direction: column;
        flex: 1;
        max-width: 240px;
        height: 500px;
    }
    .date{
        flex: 1;
        min-width: 100%;
        padding: 1rem 1rem;
        transition: all 0.2s ease-in-out;
        margin:  1rem 0;
        border-radius:  10px;

    }
    .date.active{
        flex : 2;
        background-color: #f5f5f5;
    }

    @media (max-width: 768px) {
        .sidebar{
            flex-direction: row;
            flex-wrap: nowrap;
            justify-content: stretch;
            align-items: stretch;
            align-self: stretch;
            margin: 7rem 1rem 1rem 1rem;
            max-width: 100%;
        }
        .date{
            flex: 1;
            min-width: 100%;
            padding: 1rem .5rem;
            text-align: center;
            transition: all 0.2s ease-in-out;
            margin:  1rem;
            min-width: auto;
            
        }
        .date.active{
            flex : 1;
            background-color: #f5f5f5;
        }
    }
</style>