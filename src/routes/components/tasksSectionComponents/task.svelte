<script>
    import { createEventDispatcher } from 'svelte';
    
    
    export let title;
    export let description;
    export let completed;
    export let id;
    export let date;
    // export let createdAt;

    let editable = false; 
    let cachedTitle = title;
    const dispatch = createEventDispatcher();

    async function deleteTaskRequest(id){
       
       const response = await fetch(  `http://todo-api.test/api/tasks/${id}` , {
           method: "DELETE",
           mode: "cors", 
           cache: "no-cache", 
           credentials: "same-origin", 
           headers: { "Content-Type": "application/json", "Accept": "application/json" },
       });
       
       return response.json()
    }

    async function updateTaskRequest(id, complete, title, date){
        
        const response = await fetch(  `http://todo-api.test/api/tasks/${id}` , {
            method: "PUT",
            mode: "cors", 
            cache: "no-cache", 
            credentials: "same-origin", 
            headers: { "Content-Type": "application/json", "Accept": "application/json" },
            body: JSON.stringify({
                 'completed' : complete ? 1 : 0,
                 'title' : title,
                 'date' : date
            })
        });
    
        return response.json()
    }

    function deleteTask(){
        // disabledButton = true
        
        deleteTaskRequest(id).then( data => {
            dispatch('taskDeleted', {id : id})
        })

    }
    
    function changeStatus(){
        
        dispatch('taskUpdated', {id : id, completed : !completed, title : title, date : date,} )
        
    
        updateTaskRequest(id, !completed,  title , date).then( data => {
            console.log(data)
        })
    }

    function updateTask(){

        if(title == ''){
            title = cachedTitle
        }
        dispatch('taskUpdated', {id : id, completed : completed, title : title , date : date} )

        editable = false

        updateTaskRequest(id, completed, title , date).then( data => {
            console.log(data)
        })
    }


</script>

<div class="task-item" >
    <div class="task-content">
        <div class={ completed ? "completed active" : "completed"}>
            <input type="checkbox" name="completed" id="completed" checked={completed} on:change={changeStatus}>
        </div>
        <div class="task-details">
            {#if editable === false } 
            <h3 class={completed ? "complted" : ""} on:dblclick={ () => editable = !editable}>
                {title} 
            </h3>
            {:else}
            <input type="text" bind:value={title} on:blur={updateTask} autofocus >
            {/if}
            <p>{description.slice(0, 100) }{ description.length > 100 ? '...' : ''}</p>
        </div>
        
    </div>
    <div class="options">
        <button class="btn btn-primary">edit</button>
        <button class="btn btn-danger" on:click={deleteTask}>delete</button>
    </div>
</div>

<style>
    .task-item{
        display: flex;
        justify-content: space-between;
        gap: 3rem;
    }
    .task-content{
        display: flex;
        align-items: baseline;
        gap: 1rem;
        
    }
    .task-details{
        display: flex;
        flex-direction: column;

    }

    .task-details input{
        border: none;
        outline: 1px solid transparent;
        margin: 1rem 0 .1rem 0;
        padding: .4rem;
        background-color: transparent;
        font-family: inherit;
        width: 100%;
        font-size: 1.7rem;
        font-weight: 900;
    }

    .task-details input:focus{
        outline: 1px solid #a2a2a2;
    }

    p{
        font-size: 0.8rem;
        color: #a2a2a2;
    }

    h3{
        margin: 1rem 0.1rem 0;
        border: 1px solid transparent;; 
        padding: .4rem;
        font-size: 1.7rem ;
        font-weight: 900;
    }
    h3.complted{
        text-decoration: line-through;
        color: #a2a2a2;
    }

    .options{
        display: flex;
        justify-content: flex-end;
        align-items: center;
        gap: 1.3rem;
    }
    


</style>