<script>
    export let showModal; // boolean
	let title = ''; // string
	let description = ''; // string
	let date = ''; // string
    let dialog; // HTMLDialogElement
	let error; 

	function createTask() {

	}
    $: if (dialog && showModal) dialog.showModal();
</script>

<dialog
	bind:this={dialog}
	on:close={() => (showModal = false)}
>
	<!-- svelte-ignore a11y-no-static-element-interactions -->
	<!-- <div on:stopPropagation> -->
		<form action="" on:submit|preventDefault={createTask}>
			<h1>Create a task</h1>
			{#if error}
				<div class="error">
					<p class="text-error">error : { error }</p>
				</div>
			{/if}
			<div class="input">
				<input type="text"  class="input" placeholder="task name"  bind:value={title}/>
			</div>
			<div class="input">
				<textarea name="description" id="description"  rows="10" bind:value={description}></textarea>
			</div>
			<div class="input">
				<input type="date" name="date" id="date" bind:value={date}>
			</div>
			<!-- <button on:click={() => dialog.close()}>close modal</button> -->
		</form>
	<!-- </div> -->

</dialog>

<style>
	form {
		width: 100%;
		display: flex;
		flex-direction: column;
		justify-content: center;
		align-items: stretch;
		padding: 2rem;
		gap: 1rem;
	}
	.input {
		width: 100%;
	}

	.input * {
		width: 50%;
		padding: .8rem;
		border: none;
		border-radius: 0.2em;
		outline: none;
		background-color: #f4f4f4;
	}

	.input *:hover {
		outline: 3px solid #f4f4f4;
	}

	dialog {
		width: 30%;
		overflow: hidden;
		border-radius: 0.2em;
		border: none;
	}
	
	dialog::backdrop {
		background: rgba(0, 0, 0, 0.3);
	}
	dialog[open] {
		animation: zoom 0.3s cubic-bezier(0.34, 1.56, 0.64, 1);
	}

	@keyframes zoom {
		from {
			transform: scale(0.95);
		}
		to {
			transform: scale(1);
		}
	}
	dialog[open]::backdrop {
		animation: fade 0.2s ease-out;
	}
	@keyframes fade {
		from {
			opacity: 0;
		}
		to {
			opacity: 1;
		}
	}
	button {
		display: block;
	}
</style>