<script>
	import { onMount } from 'svelte';
	import { theme } from '$lib/stores/themeStore';
	import logo from '$lib/images/svelte-logo.svg';

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

	<div class="logo-container">
		<a href="https://kit.svelte.dev/"><img src={logo} alt="svelte_logo" /></a>
	</div>

	<nav>
		<ul>
			<li><a href="/">Home</a></li>
		</ul>
	</nav>

	<select name="select-theme" bind:value={$theme}>
		<option value="system">System</option>
		<option value="light-theme">Light</option>
		<option value="dark-theme">Dark</option>
	</select>
</header>

<style>

	:global(body.dark-theme) {
			--header: var(--color-surface-mixed-300);
			--select-bg: var(--color-primary-500);
			--select-text: var(--background);
			--select-hover-bg: var(--background);
			--select-hover-text: var(--text);
	}

	:global(body.light-theme) {
      --header: var(--color-primary-500);
      --select-bg: var(--color-primary-200);
      --select-text: var(--text);
      --select-hover-bg: var(--background);
      --select-hover-text: var(--text);
	}

	header {
		background: var(--header);
		height: 50px;
		display: flex;
		justify-content: space-between;
		align-items: center;
		padding: 10px 20px;
	}

	.logo-container{
			height: 100%;
      aspect-ratio: 1/1;
	}

	img {
		height: 100%;
		width: 100%;
	}

	img:hover {
		scale: 0.9;
	}

	ul {
			list-style: none;
	}

	a:hover{
			color: var(--color-primary-500);
	}

	select {
		background: var(--select-bg);
		color: var(--select-text);
		padding: 5px;
		border: none;
	}

	select:hover {
		cursor: pointer;
		background: var(--select-hover-bg);
		color: var(--select-hover-text);
	}
</style>
