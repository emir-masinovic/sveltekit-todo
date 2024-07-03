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
	<a href="https://kit.svelte.dev/"><img src={logo} alt="svelte_logo" /></a>

	<select name="select-theme" bind:value={$theme}>
		<option value="system">System</option>
		<option value="light-theme">Light</option>
		<option value="dark-theme">Dark</option>
	</select>
</header>

<style>
	header {
		background: var(--color-surface-mixed-300);
		height: 50px;
		display: flex;
		justify-content: space-between;
		padding: 10px 20px;
	}

	img {
		height: 100%;
		width: 100%;
	}

	img:hover {
		scale: 0.9;
	}

	select {
		background: var(--color-primary-500);
		padding: 0 5px;
		border: none;
	}

	select:hover {
		cursor: pointer;
		background: var(--color-surface-100);
		color: var(--color-primary-500);
	}
</style>
