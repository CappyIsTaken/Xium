<script lang="ts">
	import * as Threlte from '@threlte/core';
	import * as Three from 'three';
	import * as Utils from 'three/src/math/MathUtils';
	import * as TweakpaneImagePlugin from 'tweakpane-image-plugin';
	import * as Extra from '@threlte/extras';
	import canvasRecord from "canvas-record"

	import { ButtonApi, Pane } from 'tweakpane';
	import { browser } from '$app/environment';
	import {v4 as uuidv4} from "uuid"


	const xium = {
		radius: 5,
		height: 5,
		url: '',
		rotationSpeed: 2
	};
	const textureLoader: Three.TextureLoader = new Three.TextureLoader();
	let texture: Three.Texture = new Three.Texture();

	const context = Threlte.useThrelte()

	const sleep = (ms) => new Promise((resolve) => setTimeout(resolve, ms));



	let { gltf } = Extra.useGltf('/xium.glb');

	if (browser) {

	const pane = new Pane({ title: 'Scene' });
	pane.registerPlugin(TweakpaneImagePlugin);
	const xiumControls = pane.addFolder({ title: 'Xium' });
	const radiusInput = xiumControls
		.addInput(xium, 'radius', {
			min: 0
		})
		.on('change', ({ value }) => {
			xium.radius = value;
		});
	const heightInput = xiumControls.addInput(xium, 'height', { min: 0 }).on('change', ({ value }) => {
		xium.height = value;
	});
	const urlInput = xiumControls
		.addInput(xium, 'url', {
			view: 'input-image'
		})
		.on('change', ({ value }) => {
			xium.url = (value as any).src;
			texture = textureLoader.load(xium.url);
			texture.flipY = false;
		});
	const rotationSpeedInput = xiumControls
		.addInput(xium, 'rotationSpeed', {
			min: 0,
			label: 'Rot Speed'
		})
		.on('change', ({ value }) => {
			xium.rotationSpeed = value as any;
		});
	xiumControls
		.addButton({
			title: 'Render as WEBM'
		})
		.on('click', async ({ target }) => {
			const btn = target as ButtonApi
			btn.title = 'Rendering...';
			btn.disabled = true
			radiusInput.disabled = true
			heightInput.disabled = true
			urlInput.disabled = true
			rotationSpeedInput.disabled = true
			const recorder = canvasRecord(context.renderer?.domElement, {
				frameRate: 60,
				filename: `${uuidv4()}.webm`
			})
			recorder.start()
			await sleep(10000)
			recorder.stop()
			recorder.dispose()
			btn.disabled = false
			radiusInput.disabled = false
			heightInput.disabled = false
			urlInput.disabled = false
			rotationSpeedInput.disabled = false
			btn.title = 'Render as WEBM';
		});
	}
</script>

<Threlte.PerspectiveCamera position={{ x: 20, y: 10, z: 20 }} fov={50}>
	<!-- Controls -->
	<Threlte.OrbitControls
		autoRotate
		autoRotateSpeed={xium.rotationSpeed}
		target={{ x: 0, y: 0, z: 0 }}
	/>
</Threlte.PerspectiveCamera>

<!-- Lights the scene equally -->
<Threlte.AmbientLight color="white" intensity={0.2} />

<!-- Light that casts a shadow -->
<Threlte.DirectionalLight color="white" intensity={1} position={{ x: 10, y: 10 }} />

{#if $gltf}
	<Threlte.Mesh
		geometry={$gltf.nodes['Cube'].geometry}
		material={new Three.MeshStandardMaterial({ map: texture })}
		scale={{ x: xium.radius, y: xium.height, z: xium.radius }}
		position={{ x: 0, y: 0, z: 0 }}
	/>
{/if}
