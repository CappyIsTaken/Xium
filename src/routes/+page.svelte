<script>
	
	import Scene from "$lib/scenes/Scene.svelte"
	import {Canvas} from "@threlte/core"
</script>

<head>
	<title>Project: Xium</title>
</head>


<div class="scene">
	<Canvas>
		<Scene/>
	</Canvas>
</div>

<style>
	.scene {
		width: 100%;
		height: 100%;
		position: absolute;
		inset: 0;
		background: radial-gradient(hsl(220 14% 20%), hsl(220 20% 10%));
		background-attachment: fixed;
	}
</style>
