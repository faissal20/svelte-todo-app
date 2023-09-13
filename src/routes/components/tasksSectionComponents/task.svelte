<script>
    import { createEventDispatcher } from 'svelte';
    import { fly } from 'svelte/transition';
    
    export let title;
    export let description;
    export let completed;
    export let id;
    export let date;
    export let key;
    
    
    let full_edit = false;
    let disabledInput;
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

    function ActiveEdit(){
        full_edit = true;
    }

    function desactivateEdit(){
        full_edit = false;
    }


</script>

{#if full_edit !== true} 
<div class="task-item" >
    <div class="task-content">
        <div class={ completed ? "completed active" : "completed"}>
            <label for="completed-{key}" class={completed ?  "completed-checkbox active" : "completed-checkbox" }>
                <i class="mi mi-check {completed ? 'visible' : ''}"></i>
                <input type="checkbox" name="completed" id="completed-{key}" checked={completed} on:change={changeStatus}>
            </label>
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
            <p>{date}</p>
        </div>
        
    </div>
    <div class="options">
        <button class="btn btn-update" on:click={ActiveEdit}>edit</button>
        <button class="btn btn-delete" on:click={deleteTask}>delete</button>
    </div>
</div>
{:else}
<div class="add-task" >
    <form action="" on:submit={ (event) => updateTask(event)}>
        <input type="text" placeholder="Add new task" class="title-input" bind:value={title} disabled={disabledInput} autofocus autocapitalize  >
        <textarea name="" id="" rows="4" cols="1" bind:value={description} placeholder="description" disabled={disabledInput} ></textarea>
        <div class="date">
            <input type="date" name="" id="" class="date-input" bind:value={date} disabled={disabledInput}>
        </div>
        <div class="action">
            <button type="submit" class="btn btn-success">Save</button>
            <button type="button" class="btn btn-secondary" on:click={ event => desactivateEdit(event) }>Close</button>
        </div>  
    </form>
</div>
{/if}

<style>
    .task-item{
        display: flex;
        justify-content: space-between;
        gap: 3rem;
        padding: 1rem 4rem 1rem 1rem;
    }

    .task-item:hover{
        background-color: #fff;
        border-radius: 10px;
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
        /* outline: 1px solid #a2a2a2; */
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
    
    .completed-checkbox input{
        visibility: hidden;
        width: 0;
        margin: 0;
        padding: 0;
    }
    .completed-checkbox{
        display: flex;
        align-items: center;
        justify-content: center;
        background: #fff;
        padding: .2rem .3rem .1rem .3rem;
        font-size: 1.3rem;
        border-radius: 10px;
        color: transparent;
        box-shadow: 0 0 0 1px #a2a2a2;
    }

    .completed-checkbox.active{
        background-color: #65ff65;
        color: #fff;
    }

</style>