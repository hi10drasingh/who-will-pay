<script>
	import Wheel from './Wheel.svelte';
</script>

<svelte:head>
	<title>Who will Pay?</title>
	<meta name="description" content="Who will pay the bill?" />
</svelte:head>

<section>
	<Wheel />
</section>

<style>
	section {
		display: flex;
		flex-direction: column;
		justify-content: center;
	}
</style>
