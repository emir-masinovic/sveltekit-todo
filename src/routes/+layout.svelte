<script>
	import { onMount } from 'svelte';
	import { theme } from '$lib/stores/themeStore';

	import logo from '$lib/images/svelte-logo.svg';
	import github from '$lib/images/github.svg';

	onMount(() => {
		theme.subscribe((value) => {
			document.body.classList.remove('light-theme', 'dark-theme', 'system');

			const systemPreferredTheme = window.matchMedia('(prefers-color-scheme: dark)').matches
				? 'dark-theme'
				: 'light-theme';

			const themeToApply = value === 'system' ? systemPreferredTheme : value;
			document.body.classList.add(themeToApply);
		});
	});
</script>

<header>
	<!-- <a href="https://github.com/emir-masinovic">
		<img src={github} alt="" width="30" height="30" />
	</a> -->

	<img class="header-item" src={logo} alt="logo_svg" />

	<select class="header-item" bind:value={$theme}>
		<option value="system">System</option>
		<option value="light-theme">Light</option>
		<option value="dark-theme">Dark</option>
	</select>
</header>

<div class="content-container">
	<slot></slot>
</div>

<style>
	:global(*) {
		margin: 0;
		padding: 0;
		box-sizing: border-box;
	}

	:root {
		font-size: 62.5%;
	}

	:global(body) {
		font-size: 1.6rem;
		font-family: 'Gill Sans', 'Gill Sans MT', Calibri, 'Trebuchet MS', sans-serif;
	}

	:global(body.light-theme) {
		--background-color: #f6f6f6;
		--text-color: #555555;
		--hover-color: gray;

		background-color: var(--background-color);
		color: var(--text-color);
	}

	:global(body.dark-theme) {
		--background-color: #4e4e4e;
		--text-color: #e6e6e6;
		--hover-color: gray;

		background-color: var(--background-color);
		color: var(--text-color);
	}

	:global(a) {
		color: inherit;
	}

	header {
		display: flex;
		justify-content: space-between;
		padding: 10px 15px;
		height: 50px;
		border-bottom: 1px solid var(--text-color);
	}

	select {
		padding: 5px;
	}

	select:hover {
		cursor: pointer;
		background-color: var(--hover-color);
	}

	.content-container {
		padding: 20px;
	}
</style>
