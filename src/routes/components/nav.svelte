<script>
    import { onMount } from "svelte";
    import { createEventDispatcher } from 'svelte';
    const dispatch = createEventDispatcher();

    let logoRotated = false;
    onMount ( () => {
        logoRotated = true;
    });


    function showModal(){
        dispatch('showModal' , {show : true});
    }
</script>

<nav>
    <div class="left-section">
        <div class={ logoRotated ? "icons clipboard" : "icons clipboard rotate" }>
            <i class="mi mi-clipboard-list"></i>
        </div>
        <h1>Todo App</h1>
    </div>

    <div class="right-section">
        <button class="icons menu">
            <i class="mi mi-menu"></i>
        </button>
    </div>
</nav>

<style>
    nav{
        display: flex;
        justify-content: space-between;
        align-items: center;
        padding: .2rem 3rem;
    }
    .left-section{
        display: flex;
        justify-content: center;
        align-items: center;
        gap: 1rem;
    }

    .right-section{
        display: flex;
        align-items: flex-start;
        gap: 1rem;
    }
    button{
        border: none;
        background: none;
        padding: 0;
        margin: 0;
        cursor: pointer;
    }

    .icons.add{
        color: #00b894;
    }

    .icons.clipboard{
        transform:  rotate(30deg);
        transition: all .4s ease-in-out;
    }

    .icons.clipboard.rotate{
        transform:  rotate(0deg);
    }

    .icons{
        font-size: 2.3rem;
        cursor: pointer;
        padding: 0;
        margin: 0;
        height: max-content;
        width: max-content;
    }
    
    h1{
        font-size: 2rem;
    }

    

</style>