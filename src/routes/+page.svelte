<script>
    import { onMount } from "svelte";
    import Nav from './components/nav.svelte'
    import Sidebar from "./components/sidebar.svelte";
    import TaskSection from "./components/taskSection.svelte";
    import moment from "moment";
    
    let tasks = []
    let start = moment(Date()).format('YYYY/MM/DD');
    let tasksDate = moment(Date()).format('YYYY/MM/DD');
    let showCreateTaskInput = false;
    let newTask = '';
    let disabledInput = false;
    let disabledButton = false;
    let processItem = null;
    let error = null;
    
   
    function showCreateTask(){
        showCreateTaskInput = true;
        setTimeout(() => {
            document.querySelector('.input').focus();
        }, 10);

    }


    async function  createTaskRequest(task){
       
        const response = await fetch(  'http://todo-api.test/api/tasks' , {
            method: "POST",
            mode: "cors", 
            cache: "no-cache", 
            credentials: "same-origin", 
            headers: { "Content-Type": "application/json" ,  "Accept": "application/json"  },
            body: JSON.stringify({ 'title' : task })
        })
        if(!response.ok){
            throw (await response.json()).message
        } 
          
        return  response
        
    }

    async function deleteTaskRequest(task){
       
            const response = await fetch(  `http://todo-api.test/api/tasks/${task.id}` , {
                method: "DELETE",
                mode: "cors", 
                cache: "no-cache", 
                credentials: "same-origin", 
                headers: { "Content-Type": "application/json", "Accept": "application/json" },
            });
            
            return response.json()
    }
       

    async function updateTaskRequest(task){
        
            const response = await fetch(  `http://todo-api.test/api/tasks/${task.id}` , {
                method: "PUT",
                mode: "cors", 
                cache: "no-cache", 
                credentials: "same-origin", 
                headers: { "Content-Type": "application/json", "Accept": "application/json" },
                body: JSON.stringify({ 'completed' : !task.completed })
            });
            
            return response.json()
    }
        


    function createTask(){
        disabledInput = true
        createTaskRequest(newTask).then(res => {
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

   

    function updateTask(task){
        disabledButton = true
        processItem = task.id
        updateTaskRequest(task).then( data => {
            let newTasks = tasks.map( item => {
                if(item.id == task.id){
                    item.completed = !item.completed
                }
                return item
            })
            tasks = newTasks
            disabledButton = false
            processItem = null
        })
    }

    function changeDate(event){
        tasksDate = event.detail.date
    }
    
</script>

<svelte:head>
    <title>Todo App</title>
	<link rel="stylesheet" href="https://unpkg.com/mono-icons@1.0.5/iconfont/icons.css" >
</svelte:head>

<Nav />
<main>
    
    
    <Sidebar start={ start } on:message={changeDate} />
    
    <TaskSection tasksDate={tasksDate} />
    
    
</main>

<!-- <div class="todo-banner">
    <div class="todo-banner-head">
        <p>you can add a new task here </p>
        <button on:click={showCreateTask}> new </button>
    </div>
    
    <form action="" on:submit|preventDefault={createTask}>
        <input type="text"  class="input" placeholder="task name" disabled={disabledInput} bind:value={newTask}/>
        {#if error}
            <p class="text-error">error : { error }</p>
        {/if}
    </form>
    
</div>

<div class="todo-list">
    {#if tasks.length > 0 }
        {#each tasks as task}
            <div class="todo-item">
                <p class={task.completed ? 'completed' : ''}>{task.title}</p>
                <dir>
                    <button on:click={updateTask(task)} class={disabledButton && processItem == task.id ? 'disable' : '' }>{task.completed ? 'uncompleted' : 'completed' }</button>
                    <button on:click={deleteTask(task)} class={disabledButton && processItem == task.id ? 'disable' : '' } >delete</button>
                </dir>
            </div>
        {:else}
        <div>
            <h2>... Loading</h2>
        </div>
        {/each}
    {:else}
        <h4>{ loading ? '... Loading' : 'No tasks' }</h4>
    {/if}
</div> -->



<style>
    main{
        display: flex;
        justify-content: flex-start;
        align-items: flex-start;
        gap: 1rem;
        width: 100%;
        height: calc(100vh - 130px);
        margin-top: 1rem;
    }

    @media (max-width: 768px){
        main{
            flex-direction: column;
            justify-content: center;
            align-items: center;
            gap: 0rem;
            width: 100%;
        }
    }
</style>