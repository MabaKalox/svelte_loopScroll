<script>
	import LoopScroll from './LoopScroll/LoopScroll.svelte'

	let hours_array = Array(24).fill().map((_, i) => {
		return {'hour': i};
	});
</script>

<main>
	
	<LoopScroll
		hours_array={hours_array}
		card_quantity={24}
		card_displayed={6}
		card_height_px={100}
		custom_className="test_Loop"
	/>
</main>

<style type="text/scss">
	$color: rgb(243, 7, 7);

	main {
		text-align: center;
		padding: 1em;
		max-width: 240px;
		margin: 0 auto;
		display: flex;
		flex-direction: column;
		& :global(.test_Loop) {
			border-color: red !important;
		}
	}

	h1 {
		color: $color;
		text-transform: uppercase;
		font-size: 2em;
		font-weight: 100;
	}

	@media (min-width: 640px) {
		main {
			max-width: none;
		}
	}

</style>