<script>
	import Hour from './Nested/Hour.svelte'
	import { onMount, afterUpdate } from 'svelte';
	const card_height_px = 50;
	let card_quantity = 24;
	const card_displayed = 12;
	let isMobile = (
		Boolean("ontouchstart" in window) ||
		Boolean(navigator.msMaxTouchPoints)
	);
	// Color
	const index_to_rgb = (color_coff, shift_coff) => {
		const twoPI = Math.PI*2;
		return 128+(
			Math.floor(127*Math.sin(twoPI*(color_coff+shift_coff/3)))
		)
	}

	let hoursArray = Array(card_quantity).fill().map((_, i, arr) => {
		const hour_def = {hour: i, color: null}
		const color_coff = i/(arr.length-1);
		const red = index_to_rgb(color_coff, 0)
		const green = index_to_rgb(color_coff, 2)
		const blue = index_to_rgb(color_coff, 1)
		hour_def.color = `rgb(${red}, ${green}, ${blue})`
		return hour_def;
	});

	const hoursArray_copy = [...hoursArray]
	const remove_hour = () => {
		hoursArray = hoursArray.slice(1)
	}
	const reset_hoursArray = () => {
		hoursArray = [...hoursArray_copy]
	}

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
			if (timeslots_wrapper.scrollTop >= card_quantity*card_height_px) {
				timeslots_wrapper.scrollTop = 1
			} else if (timeslots_wrapper.scrollTop === 0) {
				timeslots_wrapper.scrollTop = card_quantity*card_height_px-1
			}
		}
		timeslots_wrapper.addEventListener(
			'scroll',
			isMobile ? mobile_scrollHandler : pc_scrollHandler
		)
	})

	$: card_quantity = hoursArray.length

	afterUpdate(() => {
		if (isMobile) {
			const timeslots_wrapper = document.getElementById("timeslots_wrapper")
			if (timeslots_wrapper) {
				const nodes_was_visible = [...document.getElementsByClassName("copy")]
				nodes_was_visible.forEach(child => {
					child.remove()
				})
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
	<h1>Hello World!</h1>
	{#if hoursArray.length > 0}
		<button  on:click={remove_hour}>Remove Hour</button>
	{:else}
		<button on:click={reset_hoursArray}>Reset</button>
	{/if}
	<div
		id="timeslots_wrapper"
		style={`
			height: ${card_height_px*card_displayed}px;
			grid-auto-rows: ${card_height_px}px;
		`}>
		{#each hoursArray as hour_def (hour_def.hour)}
			<Hour color={hour_def.color} hour={hour_def.hour} />
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

	div {
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
	}

	@media (min-width: 640px) {
		main {
			max-width: none;
		}
	}

</style>