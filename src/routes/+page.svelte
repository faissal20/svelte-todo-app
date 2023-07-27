<script>
    import { onMount } from "svelte";
    import Nav from './components/nav.svelte'
    import Sidebar from "./components/sidebar.svelte";
    import TaskSection from "./components/taskSection.svelte";
    import moment from "moment";
    let showModal = false;

    let tasks = []
    let start = moment();
    let tasksDate = moment().format('YYYY/MM/DD');
    let showCreateTaskInput = false;
    let newTask = '';
    let disabledInput = false;
    let disabledButton = false;
    let processItem = null;
    let error = null;
    
   
    
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


    function changeDate(event){
        tasksDate = event.detail.date
    }
    

    function showModalAction(event){
        showModal = event.detail.show
    }
</script>

<svelte:head>
    <title>Todo App</title>
	<link rel="stylesheet" href="https://unpkg.com/mono-icons@1.0.5/iconfont/icons.css" >
</svelte:head>

<Nav showModal={showModal} />
<main>
    
    <Sidebar start={ start } on:message={changeDate} />
    
    <TaskSection tasksDate={tasksDate} />
    

</main>

<style>
    main{
        display: flex;
        justify-content: flex-start;
        align-items: flex-start;
        gap: 0;
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