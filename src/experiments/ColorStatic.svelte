<script>
	import { onMount } from "svelte"
	import { browser } from "$app/env"

	/** @type {HTMLCanvasElement} */
	let canvasElement
	/** @type {CanvasRenderingContext2D} */
	let ctx
	/** @type {ImageData} */
	let imageData

	let mounted = false
	let min = 100
	let max = 255

	onMount(() => {
		ctx = canvasElement.getContext("2d")
		const { width, height } = canvasElement
		imageData = ctx.getImageData(0, 0, width, height)
		mounted = true
	})

	function drawPixel(i, r, g, b) {
		imageData.data[i + 0] = r
		imageData.data[i + 1] = g
		imageData.data[i + 2] = b
		imageData.data[i + 3] = 255
	}

	function randomInt(min, max) {
		return Math.floor(Math.random() * (max - min + 1) + min)
	}

	function fillNoise() {
		for (let i = 0; i < imageData.data.length; i += 4) {
			const c1 = randomInt(min, max)
			const c2 = randomInt(min, max)
			const c3 = randomInt(min, max)

			drawPixel(i, c1, c2, c3)
		}
	}

	function render() {
		fillNoise()
		ctx.putImageData(imageData, 0, 0)
	}

	$: min, max, mounted && render()
</script>

<div>
	<canvas bind:this={canvasElement} width="300" height="300" />
	<label>
		min ({min.toString().padStart(3, "0")})
		<input type="range" min="0" max="255" step="1" bind:value={min} />
	</label>
	<label>
		max ({max.toString().padStart(3, "0")})
		<input type="range" min="0" max="255" step="1" bind:value={max} />
	</label>
</div>

<style>
</style>
