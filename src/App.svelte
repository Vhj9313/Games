<script>
	import Navbar from './Components/Navbar.svelte';
	import Footer from './Components/Footer.svelte';
	import Gamehub from './Gamehub.svelte';
</script>

<div class="layout">
	<Navbar />
	<main>
		<Gamehub />
	</main>
	<Footer />
</div>

<style>
	.layout {
		display: flex;
		flex-direction: column;
		min-height: 100vh;
		background-color: #121212;
		color: #ffffff;
	}

	main {
		flex: 1;
		padding: 2rem 1rem;
		width: 100%;
		max-width: 1280px;
		margin: 0 auto;
		box-sizing: border-box;
	}
</style>
