<script>
	import { onMount } from 'svelte';
	import {
		CheckSquare,
		CheckSquareFill,
		Plus,
		PlusCircle,
		Square,
		Trash
	} from 'svelte-bootstrap-icons';

	$: console.log('Todo array: ', todoArray);
	$: console.log(fetchError);

	let todoArray = [];
	let fetchError = null;

	async function getTodoArray() {
		try {
			const response = await fetch('/api/todo_items');
			if (!response.ok) {
				throw new Error(`${response.status} ${response.statusText}`);
			}
			todoArray = await response.json();
		} catch (error) {
			fetchError = error;
		}
	}

	async function addTodo() {
		const userInputHTML = document.getElementById('userInput');
		const pushUserInput = userInputHTML.value;
		userInputHTML.value = '';
		try {
			const response = await fetch('/api/todo_items', {
				method: 'POST',
				body: JSON.stringify({
					title: pushUserInput,
					completed: false
				})
			});
			if (!response.ok) {
				throw new Error(`${response.status} ${response.statusText}`);
			}
			if (response.ok) getTodoArray();
		} catch (error) {
			fetchError = error;
		}
	}

	async function deleteTodo(event) {
		let idToDelete = event.target.value;
		try {
			const response = await fetch(`/api/todo_items/${idToDelete}`, {
				method: 'DELETE'
			});
			if (!response.ok) throw new Error('Failed to fetch');
			else if (response.ok) getTodoArray();
		} catch (error) {
			fetchError = error;
		}
	}

	onMount(async () => {
		getTodoArray();
		// document.getElementById('todo-unit-pusher').innerText = '123';
	});
</script>

<svelte:head>
	<title>Todo</title>
	<meta name="description" content="It's a todo app..." />
</svelte:head>

<div class="todo-container">
	<div class="todo-header">
		<input type="text" id="userInput" />
		<button on:pointerdown={addTodo}>
			<Plus style="height:100%; width:100%;" />
		</button>
	</div>

	<div class="todo-body">
		{#if fetchError !== null}
			<div class="error">
				{fetchError}
			</div>
		{/if}

		<!-- <div id="todo-unit-pusher"></div> -->

		{#each todoArray as item}
			<div class="todo-unit">
				<div class="unit-head">
					<div class="unit-date">30/06/2024</div>
					<div>
						<!-- <Square /> -->
						<!-- <CheckSquareFill /> -->
						<button class="unit-delete" value={item._id.$oid} on:click={deleteTodo}>X</button>
					</div>
				</div>
				<div class="unit-body">
					{item.title}
				</div>
			</div>
		{/each}
	</div>
</div>

<style>
	:global(body.light-theme) {
	}

	:global(body.dark-theme) {
		--todo-container: #393939;
	}

	.todo-container {
		height: 100%;
		padding: 20px;
		border: 2px solid var(--text-color);
		border-radius: 5px;
		display: flex;
		flex-direction: column;
		gap: 20px;
		background: var(--todo-container);
	}

	.todo-header {
		display: flex;
		gap: 5px;
	}

	.todo-header input {
		width: 100%;
		border-radius: 5px;
		padding: 10px;
	}

	.todo-header button {
		padding: 5px 10px;
		border-radius: 5px;
		cursor: pointer;
	}

	.todo-header button:hover {
		scale: 0.95;
	}

	.todo-body {
		height: 100%;
		display: flex;
		flex-direction: column;
		gap: 10px;
		/* border: 1px solid var(--text-color); */
		overflow: auto;
	}

	.error {
		text-align: center;
	}

	.todo-unit {
		/* display: flex; */
		gap: 10px;
		justify-content: center;
		align-items: center;
		/* margin-bottom: 10px; */
		/* border: 1px solid var(--text-color); */
		/* padding: 10px; */
		height: 25%;
		overflow: auto;
	}

	.unit-head {
		display: flex;
		justify-content: space-between;
		gap: 10px;
		border-bottom: 1px solid var(--text-color);
		margin-bottom: 5px;
	}

	.todo-unit button {
		padding: 5px;
	}
</style>
