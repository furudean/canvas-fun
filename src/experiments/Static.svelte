<script>
	import { onMount } from "svelte"

	/** @type {HTMLCanvasElement} */
	let canvasElement

	function sleep(ms) {
		return new Promise((resolve) => setTimeout(resolve, ms))
	}

	function drawPixels(imageData) {
		return (i, r, g, b) => {
			imageData.data[i + 0] = r
			imageData.data[i + 1] = g
			imageData.data[i + 2] = b
			imageData.data[i + 3] = 255
		}
	}

	function fillNoise(imageData) {
		const pixel = drawPixels(imageData)
		for (let i = 0; i < imageData.data.length; i += 4) {
			const c = Math.random() > 0.5 ? 255 : 100

			pixel(i, c, c, c)
		}
	}

	onMount(() => {
		const ctx = canvasElement.getContext("2d")
		var { width, height } = canvasElement

		const buffers = Array(5)
			.fill(null)
			.map(() => ctx.createImageData(width, height))

		buffers.forEach((imageData) => fillNoise(imageData))

		async function render() {
			for (const imageData of buffers) {
				ctx.putImageData(imageData, 0, 0)
				await sleep(120)
			}
			render()
		}

		render()
	})
</script>

<canvas bind:this={canvasElement} width="200" height="200" />
