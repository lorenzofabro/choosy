<script lang="ts">
	import { RadioGroup, RadioItem, AppBar, Avatar } from '@skeletonlabs/skeleton';
	import cool from '../lib/images/cool.png';

	let value: number = 2;

	import { setPointerControls, getCenterOfTwoPoints } from 'svelte-gestures';

	export function multiTouch(node, parameters = { touchCount: 2 }) {
		const gestureName = 'multiTouch';
		let touchCenter;

		function onUp(activeEvents) {}

		function onDown(activeEvents) {
			if (activeEvents.length === parameters.touchCount) {
				const activeEventsForLoop = [...activeEvents, activeEvents[0]];
				const coordsSum = activeEvents.reduce(
					(accu, activeEvent, index) => {
						touchCenter = getCenterOfTwoPoints(node, [activeEvent, activeEventsForLoop[index + 1]]);
						accu.x += touchCenter.x;
						accu.y += touchCenter.y;
						return accu;
					},
					{ x: 0, y: 0 }
				);

				const centerCoords = {
					x: Math.round(coordsSum.x / activeEvents.length),
					y: Math.round(coordsSum.y / activeEvents.length)
				};

				node.dispatchEvent(
					new CustomEvent(gestureName, {
						detail: { center: centerCoords, target: event.target }
					})
				);
			}
		}

		return setPointerControls(gestureName, node, null, onDown, onUp);
	}

	let scale;
	let x;
	let y;

	function handler(event) {
		scale = event.detail.scale;
		x = event.detail.center.x;
		y = event.detail.center.y;
	}
</script>

<section>
	<AppBar gridColumns="grid-cols-3" slotDefault="place-self-center" slotTrail="place-content-end">
		<svelte:fragment slot="lead">
			<RadioGroup>
				{#each { length: 4 } as _, i}
					<RadioItem bind:group={value} name="justify" value={i + 2}>{i + 2}</RadioItem>
				{/each}
			</RadioGroup>
		</svelte:fragment>

		<svelte:fragment slot="trail">
			<Avatar src={cool} class="w-11" />
			<h2 class="h3">choosy</h2>
		</svelte:fragment>
	</AppBar>

	<div
		use:multiTouch={{ touchCount: 2 }}
		on:multiTouch={handler}
		style="width:500px;height:500px;border:1px solid black;"
	>
		multi touch center: x {x}, y {y}
	</div>
</section>
