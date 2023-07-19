<script>
   import moment from "moment";
   import { fly, fade } from 'svelte/transition';
   import { onMount } from "svelte";
   import Task from "./tasksSectionComponents/task.svelte";
   export let tasksDate = moment(Date()).format('YYYY/MM/DD')
//    import loadingPng from "/assets/icons8-loading-100.png" 
   
   let tasks = []
   let loading = true;

   $:{
    fetchTasks(tasksDate)
   }

   onMount(
    fetchTasks(tasksDate)
   );
    
    async function fetchTasks(date){
        loading = true
        tasks = []
		const res = await fetch(`http://todo-api.test/api/tasks?date=${date}`);
		let tasksJson = await res.json();
        tasks =  tasksJson.data
        loading = false
    }
    
    function deleteTask(event){
        let id = event.detail.id
        console.log(id)
        let newTasks = tasks.filter(task => task.id !== id)
        tasks = newTasks
    }

    function updateTask(event){
        let id = event.detail.id
        let newTasks = tasks.map( item => {
            if(item.id == id){
                item.completed = !item.completed
            }
            return item
        })
        tasks = newTasks
    }
</script>

<div class="task-section">
    <div class="task-wrapper" >
        {#if tasks.length > 0 }
            <div transition:fly={{ y: 50, duration: 200 }}> 
                {#each tasks as task}
                <Task 
                    title={task.title} 
                    description={task.description} 
                    completed={task.completed} 
                    createdAt={task.createdAt}
                    id={task.id}
                    
                    on:taskDeleted={deleteTask} 
                    on:taskUpdated={updateTask}
                    />
                {:else}
                    <h1>no data</h1>
                {/each}
            </div>
            {:else}
            <div class="load" transition:fade={{ duration: 200 }}><img src="/assets/icons8-loading-100.png"  alt="loading" ></div>
        {/if}
    </div>
</div>

<style>
    .task-section{
        flex: 3;
        padding: 1rem 4rem;
        max-height: 100%;
        width: 100%;
    }
    .task-wrapper{
        display: flex;
        flex-direction: column;
        background-color: #f4f4f4;
        padding: 1rem 2rem;
        margin: 1rem;
        overflow-y: scroll;
        scroll-behavior: smooth;
        height: 500px;
        position: relative;
    }

    .load{
        position: absolute;
        display: flex;
        top:0;
        left:0;
        width: 100%;
        height: 100%;
        justify-content: center;
        align-items: center;
        margin: auto;
    }

    .load img{
        width: 70px;
        animation: rotate 2s linear infinite;
    }

    @keyframes rotate {
        0% {
            transform: rotate(0deg);
        }
        100% {
            transform: rotate(359deg);
        }
    }

    @media (max-width: 768px) {
        .task-section{
            padding: 1rem 1rem;
        }
        .task-wrapper{
            padding: 1rem 1rem;
            margin: 0rem 1rem;
        }
    }
</style>