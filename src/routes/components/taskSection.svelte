<script>
   import moment from "moment";
   import { fly, fade } from 'svelte/transition';
   import { onMount } from "svelte";
   import Task from "./tasksSectionComponents/task.svelte";
   export let tasksDate = moment(Date()).format('YYYY/MM/DD')
//    import loadingPng from "/assets/icons8-loading-100.png" 
   
   let tasks = []
   let title;
   let description;
   let error = ''; 
   let loading = true;
   let showAddTask = false;
   let disabledInput = false 
   let months = [1,2, 3, 4, 5, 6, 7, 8, 9, 10, 11,12];
   let days = [1,2, 3, 4, 5, 6, 7, 8, 9, 10, 11,
    12, 13, 14, 15, 16, 17, 18, 19, 20, 21,
    22, 23, 24, 25, 26, 27, 28, 29, 30, 31];

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

    async function  createTaskRequest(task , description ){
       
        const response = await fetch(  'http://todo-api.test/api/tasks' , {
            method: "POST",
            mode: "cors", 
            cache: "no-cache", 
            credentials: "same-origin", 
            headers: { "Content-Type": "application/json" ,  "Accept": "application/json"  },
            body: JSON.stringify({ 
                        'title' : task,
                        'description' : description,
                    })
        })

        if(!response.ok){
            throw (await response.json()).message
        } 
            
        return  response
   }

  
       


   function createTask(event){
        event.preventDefault();
        console.log('test')
       disabledInput = true

       createTaskRequest(title , description).then(res => {
           res.json().then( data => {
               error = null
               let newTasks = tasks
               newTasks.unshift(data.data)
               tasks = newTasks
               disabledInput = false
           } )
           
       }).catch(res => {
           error = res
           disabledInput = false
           
       })
   }
    
    function deleteTask(event){
        let id = event.detail.id
        console.log(id)
        let newTasks = tasks.filter(task => task.id !== id)
        tasks = newTasks
    }

    function updateTask(event){
        let id = event.detail.id
        let completed = event.detail.completed
        let title = event.detail.title
        let taskdate = event.detail.date

        let newTasks = tasks.map( item => {
            if(item.id == id){
                item.completed = completed
                item.title = title
                item.date = taskdate
            }
            return item
        })
        tasks = newTasks
    }

    function showAddTaskSection(){
        showAddTask = true;
    }

    function hideAddTask(event){
        event.preventDefault(); 
        
        showAddTask = false;
    }
</script>

<div class="task-section">
    <div class="task-wrapper" >
        <div class="menu">
            <button on:click={showAddTaskSection}>
                <i class="mi mi-circle-add"></i>
            </button>
        </div>
        {#if showAddTask === true}
        <div class="add-task" transition:fly={{ y: -50, duration: 200 }}>
            <form action="" on:submit={ (event) => createTask(event)}>
                <input type="text" placeholder="Add new task" class="title-input" bind:value={title} disabled={disabledInput} autofocus autocapitalize  >
                <textarea name="" id="" rows="4" cols="1" bind:value={description} placeholder="description" disabled={disabledInput} ></textarea>
                <div class="date">
                    <input type="date" name="" id="" class="date-input" disabled={disabledInput}>
                </div>
                <div class="action">
                    <button type="submit" class="btn btn-primary">Save</button>
                    <button type="button" class="btn btn-secondary" on:click={ event => hideAddTask(event) }>Cancel</button>
                </div>  
            </form>
        </div>
        {/if}
        {#if tasks.length > 0 }
            <div transition:fly={{ y: 50, duration: 200 }}> 
                {#each tasks as task}
                <Task 
                    title={task.title} 
                    description={task.description} 
                    date={task.date}
                    completed={task.completed} 
                    createdAt={task.createdAt}
                    id={task.id}
                    
                    on:taskDeleted={deleteTask} 
                    on:taskUpdated={updateTask}
                    />
                    {/each}
                </div>
                {:else}
                {#if loading == false}
                    <div class="no-data">no data</div>
                {:else}
                <div class="load" transition:fade={{ duration: 200 }}><img src="/assets/icons8-loading-100.png"  alt="loading" ></div>
                {/if}
        {/if}
    </div>
</div>

<style>
    .task-section{
        flex: 3;
        padding: 1rem;
        max-height: 100%;
        width: 100%;
    }
    .task-wrapper{
        border-radius: 10px;
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

    .no-data{
        display: flex;
        justify-content: center;
        align-items: center;
        height: 100%;
        font-size: 2rem;
        font-weight: 900;
    }

    .menu {
        position: absolute;
        background-color: #ffffff;
        right : 1rem;
        bottom: 1rem;
        z-index: 1000;
        display: flex;
        justify-content: center;
        align-items: center;
        box-shadow: 0 0 40px #f4f4f4 ;
        border-radius: 14px;
        
    }
    
    .menu button{
        padding: 1rem 1rem .6rem 1rem;
        cursor: pointer;
        width: 100%;
        height: 100%;
        border: none;
        background-color: transparent;
        font-size: 2rem;
        margin: auto;
    }

    .add-task{
        border-radius: 14px;
        margin: 1rem .5rem;
        background-color: rgb(166, 166, 166);
        padding: .4rem 2rem 2.4rem 2rem; 
    }
    .add-task .title-input{
        border: none;
        outline: 1px solid transparent;
        margin: 1rem 0 .1rem 0;
        padding: .4rem;
        border-radius: 7px;
        background-color: #ffffff98;

        font-family: inherit;
        width: 100%;
        font-size: 1.7rem;  
        font-weight: 900;
    }

    .add-task .date{
        display: flex;
        max-width: 200px;
        gap: 1rem;
    }
    .add-task .date-input{
        border: none;
        outline: 1px solid transparent;
        margin: 1rem 0 .1rem 0;
        padding: .4rem .8rem;
        background-color: #ffffff98;
        border-radius: 7px;
        font-family: inherit;
        width: 100%;
        font-size: 1rem;  
        font-weight: 900;
    }
    .add-task textarea{
        border: none;
        outline: 1px solid transparent;
        margin: 1rem 0 .1rem 0;
        padding: .4rem;
        background-color: #ffffff98;
        border-radius: 7px;
        font-family: inherit;
        width: 100%;
        font-size: .8rem;
        font-weight: 900;
    }

    .action { 
        display: flex;
        justify-content: flex-start;
        align-items: center;
        gap: 2rem;
        max-width: 200px;

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
            padding:  1rem;
        }
        .task-wrapper{
            padding: 1rem 1rem;
            margin: 0rem 1rem;
        }
    }
</style>