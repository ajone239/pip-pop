<script lang="ts">
	import '../app.css';
	import favicon from '$lib/assets/favicon.svg';

	let { children } = $props();
</script>

<svelte:head>
	<link rel="icon" href={favicon} />
</svelte:head>

<div class="site-wrapper dark">
	<div class="main-content-wrapper">
		{@render children()}
	</div>
</div>

<style lang="scss">
	.site-wrapper {
		display: flex;
	}
	.main-content-wrapper {
		flex: 1;
		max-width: 45rem;
		width: 90%;
		margin: 0 auto;
		padding: 1rem;
	}
</style>
