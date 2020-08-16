<script>
	import { onMount, afterUpdate } from 'svelte';

	export let card_height_px = 50;
	export let card_quantity = 24;
	export let card_displayed = 12;

	// Determine if device support touch
	let isMobile = (
		Boolean("ontouchstart" in window) ||
		Boolean(navigator.msMaxTouchPoints)
	);

	// generaye simply array with given lenght and fill by indexes
	let hoursArray = Array(card_quantity).fill().map((_, i) => {
		return {'hour': i};
	});

	// Test functional for updating base hoursArray
	const hoursArray_copy = [...hoursArray]
	const remove_hour = () => {
		hoursArray = hoursArray.slice(1)
	}
	const reset_hoursArray = () => {
		hoursArray = [...hoursArray_copy]
	}
	// keep reactivity updateting hours quantity
	$: card_quantity = hoursArray.length
	
	onMount(() => {
		const timeslots_wrapper = document.getElementById("timeslots_wrapper")
		timeslots_wrapper.scrollTop = card_height_px*(card_quantity/2-card_displayed/2)
		let lastScroll = timeslots_wrapper.scrollTop
		let abs_scroll_delta = 0
		let skip_next_scrollEvent = false
		const pc_scrollHandler = () => {

			if (skip_next_scrollEvent) {
				skip_next_scrollEvent = false
				return null
			}

			abs_scroll_delta += Math.abs(lastScroll-timeslots_wrapper.scrollTop)
			let is_goingUP = timeslots_wrapper.scrollTop < lastScroll
			while (abs_scroll_delta >= card_height_px) {
				skip_next_scrollEvent = true
				abs_scroll_delta -= card_height_px
				if (is_goingUP) {
					timeslots_wrapper.firstElementChild.before(timeslots_wrapper.lastElementChild)
				} else {
					timeslots_wrapper.lastElementChild.after(timeslots_wrapper.firstElementChild)
				}
			}
			lastScroll = timeslots_wrapper.scrollTop
		}
		const mobile_scrollHandler = () => {
			if (timeslots_wrapper.scrollTop >= card_quantity*card_height_px-1) {
				timeslots_wrapper.scrollTop = 2
			} else if (timeslots_wrapper.scrollTop <= 1) {
				timeslots_wrapper.scrollTop = card_quantity*card_height_px-2
			}
		}
		timeslots_wrapper.addEventListener(
			'scroll',
			isMobile ? mobile_scrollHandler : pc_scrollHandler
		)
	})

	// example how to reactivly update hours copyes
	afterUpdate(() => {
		if (isMobile) {
			const timeslots_wrapper = document.getElementById("timeslots_wrapper")
			if (timeslots_wrapper) {
				// remove old copyes
				const nodes_was_visible = [...document.getElementsByClassName("copy")]
				nodes_was_visible.forEach(child => {
					child.remove()
				})
				// create new clones
				const nodes_visable_at_zeroScroll = [...timeslots_wrapper.children].slice(
					0, card_displayed
				)
				nodes_visable_at_zeroScroll.forEach(chield => {
					const chield_copy = chield.cloneNode(true)
					timeslots_wrapper.appendChild(chield_copy)
					chield_copy.classList.add("copy")
				})
			}	
		}
	})

</script>

<main>
	{#if hoursArray.length > 0}
		<button  on:click={remove_hour}>Remove Hour</button>
	{:else}
		<button on:click={reset_hoursArray}>Reset</button>
	{/if}
	<div
		id="timeslots_wrapper"
		class="timeslots_wrapper"
		style={`
			height: ${card_height_px*card_displayed}px;
			grid-auto-rows: ${card_height_px}px;
		`}>
		{#each hoursArray as hour_def (hour_def.hour)}
			<div class="card-wrapper">
				<div>{hour_def.hour}</div>
			</div>
		{/each}
	</div>
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
	}

	h1 {
		color: $color;
		text-transform: uppercase;
		font-size: 2em;
		font-weight: 100;
	}

	.timeslots_wrapper {
		position: relative;
		display: grid;
		border: 4px double rgb(94, 94, 94);
		width: 300px;
		overflow: auto;
		align-self: center;
		// -ms-overflow-style: none;
		// scrollbar-width: none;
		// z-index: 0;
		// &::-webkit-scrollbar {
		// 	display: none;
		// }
		& > .card-wrapper {
			margin: 0;
			display: flex;
			justify-content: center;
			align-items: center;
			border: 1px solid black;
			box-sizing: border-box;
		}
	}

	@media (min-width: 640px) {
		main {
			max-width: none;
		}
	}

</style>