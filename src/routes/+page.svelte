<script>
	import { onMount } from 'svelte';
	import convert from 'color-convert';
	import '../app.css';

	let canvasSatValue;
	let canvasHue;
	// const ctxCanvasSaturationValue;
	// const ctxCanvasHue;

	let canvasSatValueWidth = 500;
	let canvasSatValueHeigt = 300;
	let canvasHueWidth = 500;

	let color = '#aa1aaa';
	$: hue = convert.hex.hsv(color)[0];
	$: value = convert.hex.hsv(color)[1];
	$: saturation = convert.hex.hsv(color)[2];

	onMount(() => {
		drawCanvas();

		canvasHue.addEventListener('mousedown', function (event) {
			const rect = canvasHue.getBoundingClientRect();
			const x = event.clientX - rect.left;
			hue = Math.round((x / canvasHue.width) * 360);
			updateColorHue();
		});

		canvasSatValue.addEventListener('mousedown', function (event) {
			const rect = canvasSatValue.getBoundingClientRect();
			const x = event.clientX - rect.left;
			const y = event.clientY - rect.top;
			saturation = Math.round((x / canvasSatValue.width) * 100);
			value = Math.round((1 - y / canvasSatValue.height) * 100);
			updateColorSaturationValue();
		});
	});

	const drawCanvas = () => {
		const ctxCanvasSaturationValue = canvasSatValue.getContext('2d');
		const ctxCanvasHue = canvasHue.getContext('2d');

		var gradB = ctxCanvasSaturationValue.createLinearGradient(0, 0, 0, canvasSatValueHeigt);
		gradB.addColorStop(0, 'white');
		gradB.addColorStop(1, 'black');
		var gradC = ctxCanvasSaturationValue.createLinearGradient(0, 0, canvasSatValueWidth, 0);
		gradC.addColorStop(0, `hsla(${hue},100%,50%,0)`);
		gradC.addColorStop(1, `hsla(${hue},100%,50%,1)`);

		ctxCanvasSaturationValue.fillStyle = gradB;
		ctxCanvasSaturationValue.fillRect(0, 0, canvasSatValueWidth, canvasSatValueHeigt);
		ctxCanvasSaturationValue.fillStyle = gradC;
		ctxCanvasSaturationValue.globalCompositeOperation = 'multiply';
		ctxCanvasSaturationValue.fillRect(0, 0, canvasSatValueWidth, canvasSatValueHeigt);
		ctxCanvasSaturationValue.globalCompositeOperation = 'source-over';

		// Draw a color gradient on the canvas
		const gradientHue = ctxCanvasHue.createLinearGradient(0, 0, canvasHue.width, 0);
		gradientHue.addColorStop(0, 'red');
		gradientHue.addColorStop(0.17, 'yellow');
		gradientHue.addColorStop(0.34, 'lime');
		gradientHue.addColorStop(0.51, 'cyan');
		gradientHue.addColorStop(0.68, 'blue');
		gradientHue.addColorStop(0.85, 'magenta');
		gradientHue.addColorStop(1, 'red');
		ctxCanvasHue.fillStyle = gradientHue;
		ctxCanvasHue.fillRect(0, 0, canvasHue.width, canvasHue.height);
		ctxCanvasHue.strokeStyle = 'white';
	};

	function updateColorHue() {
		const hsv = convert.hex.hsv(color);
		hsv[0] = hue;
		color = '#' + convert.hsv.hex(hsv);
		drawCanvas();
	}

	function updateColorSaturationValue() {
		const hsv = convert.hex.hsv(color);
		hsv[1] = saturation;
		hsv[2] = value;
		color = '#' + convert.hsv.hex(hsv);
		drawCanvas();
	}
</script>

<div class="flex flex-col items-center justify-center bac">
	<h1 class="text-white font-bold text-4xl p-14">pickcolor.site</h1>
	<div
		class="flex flex-col sm:flex-row items-start justify-center rounded-md drop-shadow-2xl bg-color p-4"
	>
		<div class="mr-4 flex flex-row sm:flex-col text-white">
			<div style="background-color:{color}; height: 100px" class="rounded w-1/2 sm:w-full mb-4" />
			<!-- <p class="pb-2 text-white text-sm">Chosen color {color}</p> -->
			<div class="flex flex-row mb-4">
				<div class="flex flex-col justify-stretch text-right">
					<!-- <div class="mb-1 text-white text-sm p-1 border border-transparent">Hue:</div>
					<div class="mb-1 text-white text-sm p-1 border border-transparent">Value:</div>
					<div class="mb-1 text-white text-sm p-1 border border-transparent">Saturation:</div> -->
					<div class="mb-1 text-white text-sm p-1 border border-transparent">HEX:</div>
					<div class="mb-1 text-white text-sm p-1 border border-transparent">RGB:</div>
					<div class="mb-1 text-white text-sm p-1 border border-transparent">HSV:</div>
					<div class="mb-1 text-white text-sm p-1 border border-transparent">HSL:</div>
					<div class="mb-1 text-white text-sm p-1 border border-transparent">CMYK:</div>
				</div>

				<div class="flex flex-col justify-stretch text-sm">
					<!-- <div class="mb-1 rounded border p-1">{hue}</div>
					<div class="mb-1 rounded border p-1">{value}</div>
					<div class="mb-1 rounded border p-1">{saturation}</div> -->
					<div class="mb-1 rounded border p-1">
						<input class="bg-color" bind:value={color} maxlength="7" />
					</div>
					<div class="mb-1 rounded border p-1">{convert.hex.hsv(color)}</div>
					<div class="mb-1 rounded border p-1">{convert.hex.rgb(color)}</div>
					<div class="mb-1 rounded border p-1">{convert.hex.hsl(color)}</div>
					<div class="mb-1 rounded border p-1">{convert.hex.cmyk(color)}</div>
				</div>
			</div>
		</div>

		<!-- <canvas width="500" height="10" bind:this={canavasShade} /> -->
		<div>
			<div class="relative mb-4">
				<canvas
					width={canvasSatValueWidth}
					height={canvasSatValueHeigt}
					bind:this={canvasSatValue}
					class="rounded"
				/>
				<div
					style="width: 16px;height: 16px;border: 3px solid white;border-radius: 50%;background-color: {color}; position: absolute; left: {(canvasSatValueWidth /
						100) *
						value -
						8}px; top: {(canvasSatValueHeigt / 100) * (100 - saturation) - 8}px"
				/>
			</div>
			<div class="relative">
				<canvas width={canvasHueWidth} height="10" bind:this={canvasHue} class="rounded-full" />
				<div
					style="width: 16px;height: 16px;border: 3px solid white;border-radius: 50%;background-color: {'#' +
						convert.hsv.hex(hue, 100, 100)}; position: absolute; left: {(canvasHueWidth / 360) *
						hue -
						8}px; top: -3px"
				/>
			</div>
		</div>
	</div>
</div>

<style>
</style>
