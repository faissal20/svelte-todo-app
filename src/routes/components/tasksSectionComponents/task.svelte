<script>
    import { createEventDispatcher } from 'svelte';
    
    
    export let title;
    export let description;
    export let completed;
    // export let createdAt;
    export let id;
    
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

    async function updateTaskRequest(id, completed){
        
        const response = await fetch(  `http://todo-api.test/api/tasks/${id}` , {
            method: "PUT",
            mode: "cors", 
            cache: "no-cache", 
            credentials: "same-origin", 
            headers: { "Content-Type": "application/json", "Accept": "application/json" },
            body: JSON.stringify({ 'completed' : !completed })
        });
        return response.json()
    }

    function deleteTask(){
        // disabledButton = true
        
        deleteTaskRequest(id).then( data => {
            dispatch('taskDeleted', {id : id} )
        })
    }

    function changeStatus(){
        
        dispatch('taskUpdated', {id : id} )
        updateTaskRequest(id, completed).then( data => {
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
            <h3 class={completed ? "complted" : ""}>
                {title} 
            </h3>
            <p>{description}</p>
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
        gap: 2.8rem;
        
    }
    .options{
        flex: 3;
        display: flex;
        justify-content: flex-end;
        align-items: center;
        gap: 1.3rem;
    }
    
    p{
        font-size: 0.8rem;
        color: #a2a2a2;
    }

    h3.complted{
        text-decoration: line-through;
        color: #a2a2a2;
    }


</style>