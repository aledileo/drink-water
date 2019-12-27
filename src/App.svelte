<script>
	let name = 'Ale'
	let waterGlassesCount = 0;
	function handleClick() {
		waterGlassesCount += 1
	}
</script>

<main>
	<h1>Hello {name}!</h1>
	<p>You've drinked {waterGlassesCount} glasses of water so far!</p>
	<button on:click={handleClick}>Add glass of water</button>
</main>

<style>
	main {
		text-align: center;
		padding: 1em;
		max-width: 240px;
		margin: 0 auto;
	}

	h1 {
		color: #ff3e00;
		text-transform: uppercase;
		font-size: 4em;
		font-weight: 100;
	}

	@media (min-width: 640px) {
		main {
			max-width: none;
		}
	}
</style>