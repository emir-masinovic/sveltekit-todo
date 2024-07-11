<script>
	import AppCard from '../lib/components/AppCard.svelte';
</script>


<div class="app-container">
	<AppCard name="todo" 		image="./src/lib/images/todo.png" 		link="./todo" />
	<AppCard name="clicker" image="./src/lib/images/clicker.png" 	link="./clicker" />
</div>

<style>
	.app-container {
			margin: 20px;
			display: flex;
			flex-wrap: wrap;
			justify-content: center;
			gap: 20px;
	}
</style>