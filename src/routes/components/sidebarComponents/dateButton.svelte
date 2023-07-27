<script>
    import moment from 'moment';
    import { createEventDispatcher } from 'svelte';
    import { onMount } from 'svelte';

    const dispatch = createEventDispatcher(); 
    
    export let id;
    export let active;
    export let day;

    let text = '';
    
    onMount ( () => {
        switch(day){
            case moment().subtract(1, 'days').format('YYYY/MM/DD'):
                text =  'Yesterday';
                break;
            case  moment().format('YYYY/MM/DD') :
                text =  'Today';
                break;
            case moment().add(1, 'days').format('YYYY/MM/DD'):
                text =  'Tomorrow';
                break;
            case moment().add(2, 'days').format('YYYY/MM/DD'):
                text =  'Day after' 
                break;
            default: 
                text = moment(day).format('DD MMMM');
        }
    });



    function changeStatus(){
        dispatch('message', {
            active: active,
            day: day,
            id: id
		});
    }

</script>

<button on:click={changeStatus}>
    { text  }
</button>
<h4>
    { day }
</h4>

<style>
    button{
        border: none;
        background-color: transparent;
        outline: none;
        font-family: inherit;
        font-size: 1.7rem;
        font-weight: 900;
        padding: 0;
        cursor: pointer;
    }

    @media (max-width: 768px) {
        button{
            font-size: 1rem;
        }
    }
</style>