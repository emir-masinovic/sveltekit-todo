<script>
	import { onMount } from 'svelte';
	import { CheckSquare, CheckSquareFill, Square, Trash } from 'svelte-bootstrap-icons';
	import { v4 as uuidv4 } from 'uuid';

	// $: console.log('Todo array: ', todoArray);
	// $: console.log(fetchError);

	let todoArray = [];
	let fetchError = null;

	// let todoArray = [
	// 	{
	// 		_id: {
	// 			$oid: 'c2b4d657-7bd6-4546-b5f1-21a61e995ae9'
	// 		},
	// 		owner_user_id: null,
	// 		title: '1',
	// 		completed: false
	// 	},
	// 	{
	// 		_id: {
	// 			$oid: '97d66eec-052b-4036-aa37-8dde5e70589d'
	// 		},
	// 		owner_user_id: null,
	// 		title: '2',
	// 		completed: false
	// 	},
	// 	{
	// 		_id: {
	// 			$oid: '1dd7f7ae-5402-4b1c-b2ef-2cdb742851da'
	// 		},
	// 		owner_user_id: null,
	// 		title: '3',
	// 		completed: false
	// 	},
	// 	{
	// 		_id: {
	// 			$oid: 'b50625b3-8818-4794-86e0-0843bc87c296'
	// 		},
	// 		owner_user_id: null,
	// 		title: '4',
	// 		completed: false
	// 	},
	// 	{
	// 		_id: {
	// 			$oid: '4b57cb55-4afc-4930-8569-2d1ec0be0f22'
	// 		},
	// 		owner_user_id: null,
	// 		title: '5',
	// 		completed: false
	// 	}
	// ];

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
		const userInput = userInputHTML.value;
		userInputHTML.value = '';

		if (userInput === '') return;

		const newUserTodo = {
			_id: {
				$oid: uuidv4()
			},
			owner_user_id: null,
			title: userInput,
			completed: false
		};

		// Updated list until fetch syncs with it (also not persistent)
		todoArray = [...todoArray, newUserTodo];

		try {
			const response = await fetch('/api/todo_items', {
				method: 'POST',
				body: JSON.stringify({
					title: userInput,
					completed: false
				})
			});
			if (!response.ok) {
				throw new Error(`${response.status} ${response.statusText}`);
			}
			getTodoArray();
		} catch (error) {
			fetchError = error;
		}
	}

	async function deleteTodo(event) {
		const idToDelete = event.currentTarget.closest('button').value;

		// Filter it out locally, then sync with server
		// Server response updates the list with a lag
		todoArray = todoArray.filter((item) => item._id.$oid !== idToDelete);

		try {
			const response = await fetch(`/api/todo_items/${idToDelete}`, {
				method: 'DELETE'
			});
			if (!response.ok) {
				throw new Error(`${response.status} ${response.statusText}`);
			}
			getTodoArray();
		} catch (error) {
			fetchError = error;
		}
	}

	function checkboxTodo(event) {
		const todoStatusChange = event.currentTarget.closest('button').value;

		todoArray = todoArray.map((todo) => {
			if (todo._id.$oid === todoStatusChange) return { ...todo, completed: !todo.completed };
			return todo;
		});
	}

	onMount(async () => {
		getTodoArray();
	});
</script>

<svelte:head>
	<title>Todo App</title>
	<meta name="description" content="Todo app" />
</svelte:head>

<div class="wrapper">
	<div class="todo-container">
		<div class="todo-header">
			<input on:keypress={addTodo} type="text" id="userInput" />
			<button on:pointerdown={addTodo}>+</button>
		</div>

		<div class="todo-body">
			{#if fetchError !== null}
				<div class="error" style="text-align: center;">
					{fetchError}
				</div>
			{/if}

			{#each todoArray as item}
				<div class="todo-item">
					<div class="item-head">
						<div>30/06/2024</div>

						<div class="item-buttons">
							{#if item.completed === false}
								<button class="item-button" value={item._id.$oid} on:pointerdown={checkboxTodo}>
									<Square style="height: 100%; width: 100%;" />
								</button>
							{:else}
								<button class="item-button" value={item._id.$oid} on:pointerdown={checkboxTodo}>
									<CheckSquareFill style="height: 100%; width: 100%;" />
								</button>
							{/if}

							<button class="item-button" value={item._id.$oid} on:pointerdown={deleteTodo}>
								<Trash style="height: 100%; width: 100%;" />
							</button>
						</div>
					</div>

					<div class="item-body">
						{item.title}
					</div>
				</div>
			{/each}
		</div>
	</div>

	<div class="pagination">
		<a href="#">&laquo;</a>
		<a href="#">1</a>
		<a href="#" class="active">2</a>
		<a href="#">3</a>
		<a href="#">&raquo;</a>
	</div>
</div>

<style>
	.wrapper {
		height: calc(100dvh - 50px);
		width: clamp(0px, 600px, 100%);
		padding: 20px;
		margin: 0 auto;
		display: flex;
		flex-direction: column;
		justify-content: space-between;
		gap: 20px;
	}

	.todo-container {
		overflow: hidden;
	}

	.todo-header {
		display: flex;
		gap: 10px;
		margin-bottom: 20px;
	}

	.todo-header input {
		width: 100%;
		padding: 10px;
		border: none;
		font-family: inherit;
		font-size: inherit;
	}

	.todo-header button {
		background: var(--color-primary-500);
		padding: 0 20px;
		border: none;
		font-size: inherit;
		cursor: pointer;
	}

	.todo-header button:hover {
		background: var(--color-surface-300);
		color: var(--color-primary-500);
		scale: 0.9;
	}

	.todo-item {
		background: var(--color-surface-mixed-300);
		padding: 10px;
		margin-bottom: 10px;
	}

	.item-head {
		display: flex;
		justify-content: space-between;
		align-items: center;
		gap: 10px;
		border-bottom: 1px solid var(--color-surface-600);
		margin-bottom: 5px;
		padding-bottom: 5px;
	}

	.item-buttons {
		display: flex;
		gap: 5px;
	}

	.item-button {
		color: var(--text-color);
		width: 18px;
		height: 18px;
		border: none;
		cursor: pointer;
		background: none;
	}

	.item-button:hover {
		color: var(--color-primary-500);
		scale: 0.95;
	}

	.pagination {
		display: flex;
		text-align: center;
		gap: 5px;
	}

	.pagination a {
		background: var(--color-surface-mixed-300);
		padding: 5px 0px;
		width: 20%;
	}

	.pagination a.active {
		background: var(--color-surface-mixed-400);
		color: var(--color-primary-300);
	}

	.pagination a:hover:not(.active) {
		background: var(--color-primary-500);
		color: var(--color-surface-200);
	}
</style>
