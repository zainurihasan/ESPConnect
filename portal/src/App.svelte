<script>
	import {
		onMount
	} from 'svelte';
	import Connect from './components/Connect.svelte';
	import SelectScan from './components/SelectScan.svelte';
	import Status from './components/Status.svelte';

	let data = {
		loading: true,
		connectStatus: {
			sent: false,
			success: true
		},
		selection: {
			direct_connect: false,
			selected: false,
			ssid: ''
		},
		access_points: []
	}

	function setConnectSuccess(){
		data.connectStatus.sent = true;
		data.connectStatus.success = true;
	}

	function setConnectError(){
		data.connectStatus.sent = true;
		data.connectStatus.success = false;
	}

	function clearSelection() {
		data.selection.selected = false;
		data.selection.direct_connect = false;
		data.selection.ssid = "";
	}

	function selectAccessPoint(event) {
		data.selection.ssid = event.detail.ssid
		if(event.detail.open){
			data.selection.direct_connect = true
		}
		data.selection.selected = true;
	}

	async function refresh (){ 
		data.loading = true
		await updateAccessPoints()
	}

	async function updateAccessPoints() {
		const res = await fetch(`/espconnect/scan`);
		if (res.status === 200) {
			data.access_points = await res.json();
			data.loading = false;
		}else if(res.status === 202) {
			setTimeout(updateAccessPoints, 2000);
		}
		return res;
	}

	onMount(async () => {
		try {
			await updateAccessPoints();
		} catch (err) {
			console.log(err);
		}
	});
</script>

<div class="container main-container">
	<div class="row">
		<div class="column text-center">
			<svg id="svg" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" width="250" height="100" viewBox="0, 0, 400,133.33333333333334" class="logo"><g id="svgg"><path id="path0" d="M0.000 7.566 C 0.000 8.140,0.326 8.812,0.725 9.058 C 1.123 9.304,1.449 10.099,1.449 10.824 C 1.449 11.548,1.931 12.541,2.519 13.029 C 3.107 13.517,4.455 15.758,5.515 18.009 C 6.574 20.260,7.678 22.264,7.967 22.464 C 8.257 22.663,9.074 23.967,9.783 25.362 C 10.492 26.757,11.271 28.062,11.515 28.261 C 11.758 28.460,12.773 30.183,13.769 32.090 C 16.901 38.085,17.952 39.919,18.978 41.173 C 19.525 41.843,20.745 43.859,21.689 45.652 C 22.632 47.446,23.844 49.428,24.383 50.057 C 24.922 50.686,25.362 51.706,25.362 52.323 C 25.362 52.941,25.870 53.867,26.491 54.383 C 27.112 54.898,28.088 56.324,28.660 57.551 C 29.233 58.778,30.525 61.087,31.532 62.681 C 32.540 64.275,33.578 66.069,33.839 66.667 C 34.101 67.264,35.089 68.998,36.037 70.518 C 36.984 72.039,38.394 74.403,39.169 75.772 C 39.945 77.141,41.214 79.381,41.990 80.750 C 43.745 83.848,46.443 88.366,47.659 90.245 C 48.166 91.027,49.053 92.694,49.632 93.950 C 50.210 95.205,51.101 96.678,51.610 97.223 C 52.119 97.768,53.351 99.833,54.348 101.812 C 55.344 103.790,56.567 105.846,57.065 106.379 C 57.563 106.913,57.971 107.816,57.971 108.385 C 57.971 108.954,58.297 109.420,58.696 109.420 C 59.094 109.420,59.420 109.909,59.420 110.507 C 59.420 111.105,59.675 111.594,59.986 111.594 C 60.297 111.594,60.937 112.491,61.407 113.587 C 62.496 116.124,66.436 123.124,67.490 124.394 C 68.138 125.175,74.353 125.362,99.545 125.359 L 130.797 125.355 133.786 120.830 C 135.430 118.341,137.645 114.837,138.709 113.043 C 143.897 104.292,144.655 103.317,145.190 104.711 C 145.419 105.308,145.957 105.797,146.386 105.797 C 146.814 105.797,148.044 106.612,149.118 107.609 C 150.193 108.605,151.448 109.420,151.908 109.420 C 152.367 109.420,152.902 109.828,153.095 110.326 C 153.289 110.824,155.199 112.169,157.339 113.314 C 159.480 114.459,162.548 116.126,164.157 117.018 C 165.765 117.909,170.168 119.632,173.939 120.845 C 188.290 125.462,186.637 125.362,248.991 125.362 L 305.797 125.362 305.797 108.333 L 305.797 91.304 250.112 91.304 C 188.455 91.304,190.509 91.450,183.149 86.550 C 179.123 83.870,174.638 79.647,174.638 78.537 C 174.638 78.053,174.293 77.542,173.871 77.401 C 172.865 77.066,170.290 69.215,170.290 66.485 C 170.290 57.517,177.289 47.864,186.747 43.787 C 193.939 40.687,195.058 40.632,251.630 40.606 L 305.797 40.580 305.797 23.551 L 305.797 6.522 217.895 6.522 L 129.992 6.522 126.047 14.312 C 123.877 18.596,121.901 22.264,121.656 22.464 C 121.410 22.663,120.964 23.641,120.664 24.638 C 120.364 25.634,119.713 26.938,119.218 27.536 C 118.723 28.134,117.243 30.836,115.930 33.541 C 114.617 36.246,113.267 38.630,112.931 38.838 C 112.594 39.046,112.319 39.798,112.319 40.510 C 112.319 41.222,111.667 42.395,110.870 43.116 C 110.072 43.837,109.420 44.871,109.420 45.413 C 109.420 45.955,108.365 48.269,107.075 50.554 C 105.785 52.840,103.746 56.640,102.546 58.998 C 100.427 63.159,98.551 64.968,98.551 62.849 C 98.551 61.941,97.811 60.368,94.368 53.958 C 93.948 53.176,92.641 50.580,91.463 48.188 C 89.589 44.382,86.015 37.587,82.887 31.884 C 82.340 30.888,81.891 29.692,81.889 29.227 C 81.886 28.762,81.630 28.273,81.319 28.140 C 80.834 27.932,77.020 20.771,71.455 9.619 L 69.928 6.556 34.964 6.539 C 4.996 6.524,0.000 6.671,0.000 7.566 M323.188 65.942 L 323.188 125.362 359.058 125.362 L 394.928 125.362 394.928 65.942 L 394.928 6.522 359.058 6.522 L 323.188 6.522 323.188 65.942 M346.377 26.087 L 346.377 32.609 364.855 32.609 L 383.333 32.609 383.333 38.406 L 383.333 44.203 364.855 44.203 L 346.377 44.203 346.377 50.362 L 346.377 56.522 340.217 56.522 L 334.058 56.522 334.058 38.043 L 334.058 19.565 340.217 19.565 L 346.377 19.565 346.377 26.087 M196.558 50.120 C 194.864 50.500,193.478 51.117,193.478 51.492 C 193.478 51.867,193.071 52.183,192.572 52.194 C 191.089 52.227,186.161 56.290,184.321 58.997 C 180.662 64.383,183.038 74.698,188.614 77.634 C 189.496 78.098,190.381 78.674,190.580 78.913 C 193.398 82.291,192.415 82.233,250.543 82.449 L 305.072 82.653 305.072 65.964 L 305.072 49.275 252.355 49.353 C 221.948 49.398,198.334 49.722,196.558 50.120 M372.334 61.566 C 372.472 61.980,373.076 62.319,373.676 62.319 C 375.186 62.319,380.400 66.101,380.419 67.210 C 380.428 67.708,380.679 68.118,380.978 68.122 C 383.407 68.147,384.286 81.109,382.065 84.150 C 381.368 85.105,380.442 86.535,380.008 87.327 C 377.379 92.129,365.052 94.887,357.995 92.252 C 343.036 86.666,343.020 66.466,357.971 61.477 C 362.257 60.047,371.847 60.107,372.334 61.566 M357.715 73.516 C 355.259 75.792,355.283 77.973,357.790 80.331 C 362.428 84.693,374.638 82.037,374.638 76.666 C 374.638 71.775,362.109 69.443,357.715 73.516 M383.333 106.159 L 383.333 112.319 358.696 112.319 L 334.058 112.319 334.058 106.159 L 334.058 100.000 358.696 100.000 L 383.333 100.000 383.333 106.159 " stroke="none" fill="#000000" fill-rule="evenodd"></path></g></svg>
			<!-- <svg xmlns="http://www.w3.org/2000/svg" width="64" height="64" viewBox="0 0 24 24" class="logo"><path d="M5 12L3 12 3 21 12 21 12 19 5 19zM12 5L19 5 19 12 21 12 21 3 12 3z"></path></svg> -->
		</div>
	</div>
	<div class="row mb-2">
		<div class="column">
			<div class="card">
				{#if data.loading}
					<div class="container">
						<div class="row h-100">
							<div class="column text-center d-flex h-100">
								<div class="loader">
									
									<svg xmlns="http://www.w3.org/2000/svg" width="36" height="36" viewBox="0 0 24 24" style="fill: currentColor"><path d="M12,22c5.421,0,10-4.579,10-10h-2c0,4.337-3.663,8-8,8s-8-3.663-8-8c0-4.336,3.663-8,8-8V2C6.579,2,2,6.58,2,12 C2,17.421,6.579,22,12,22z"></path></svg>
								</div>
							</div>
						</div>
					</div>
				{:else}
					{#if !data.connectStatus.sent}
						{#if !data.selection.selected}
							<SelectScan access_points={data.access_points} on:refresh={refresh} on:select={selectAccessPoint} />
						{:else}
							<Connect ssid={data.selection.ssid} direct_connect={data.selection.direct_connect} on:back={clearSelection} on:success={setConnectSuccess} on:error={setConnectError} />
						{/if}
					{:else}
							<Status success={data.connectStatus.success} />
					{/if}
				{/if}
			</div>
		</div>
	</div>
	<div class="row">
		<div class="column text-sm text-muted">
			<p class="text-center">
				<!-- Made with ❤️ by <a href="https://github.com/ayushsharma82" target="_blank">ayushsharma82</a>. -->
			</p>
			<p class="text-center">
				<!-- <a href="https://www.buymeacoffee.com/6QGVpSj" target="_blank">Buy me a coffee ☕</a> -->
			</p>
		</div>
	</div>
</div>

<style type="text/scss" global>
	$color-primary: #353a41;
	$color-secondary: #202327;
	$color-muted: #626770;
	$color-quinary: #869ab8;
	
	$loader-color: #fff;
	$loader-size: 8px;
	$loader-height: 4px;
	$loader-border-size: 4px;
	$loader-gap: 6px;
	$loader-animation-duration: 1s;

	@import "../node_modules/milligram/src/milligram.sass";
	@import "../node_modules/spinthatshit/src/variables.scss";
	@import "../node_modules/spinthatshit/src/animations.scss";
	@import "../node_modules/spinthatshit/src/loaders/_loader10.scss";

	.text-muted{
		color: $color-muted !important;
	}

	.text-center{
		text-align: center;
	}

	.w-100{
		width: 100%;
	}

	.h-100{
		height: 100%;
	}

	.d-flex{
		display: flex !important;
	}

	.flex-columns{
		flex-direction: column;
	}

	.flex-rows{
		flex-direction: row;
	}

	.mb-2{
		margin-bottom: 2rem;
	}

	.pr-1{
		padding-right: 1rem;
	}

	.w-auto{
		width: auto !important;
	}

	.text-sm{
		font-size: 12px;
	}

	.clickable-row{
		padding: 1rem 0rem;
		border-bottom: 0.1rem solid #f4f5f6;
		transition: background-color .5s cubic-bezier(0.215, 0.610, 0.355, 1), box-shadow .5s cubic-bezier(0.215, 0.610, 0.355, 1);
		border-radius: .5rem;
		cursor: pointer;
		word-break: break-word;

		&:hover{
			box-shadow: rgba(99, 99, 99, 0.1) 0px 2px 8px 2px;
		}
	}

	input[type=text], input[type=password]{
		padding: 2.5rem 2rem !important;
		box-shadow: rgba(204, 219, 232, 0.2) 0px 3px 6px 1px inset, rgba(255, 255, 255, 0.4) 0px 0px 6px 6px inset;
		border: none;
		font-size: 16px !important;
	}

	input:disabled{
		background-color: rgba(47, 63, 82, 0.05);
		border: none;
	}

	.logo{
		margin: 5rem 1rem;
		fill: currentColor;
	}

	.main-container{
		max-width: 42rem !important;
		margin: 3rem auto;
	}

	.card{
		display: flex;
		position: relative;
		padding: 1rem;
		border-radius: 1rem;
		width: 100%;
		box-shadow: rgba(149, 157, 165, 0.15) 0px 8px 24px;
		min-height: 290px;

		.container{
			padding: 1rem 2rem;
		}
	}

	.button{
		border-radius: .5rem;
		transition: background-color .5s linear;
		box-shadow: rgba(149, 157, 165, 0.15) 0px 8px 24px !important;
		&[disabled]{
			opacity: 0.9;
		}
	}

	.btn-light{
		background-color:rgb(113, 121, 129) !important;
		border-color: rgb(113, 121, 129) !important;
	}

	.loader{
		margin: auto;
		svg {
			animation-name: spin;
			animation-duration: 1500ms;
			animation-iteration-count: infinite;
			animation-timing-function: linear; 
		}
	}

	@keyframes spin {
    from {
        transform:rotate(0deg);
    }
    to {
        transform:rotate(360deg);
    }
	}

	.btn-loader{
		margin: auto;
		@include loader10;
	}

</style>
