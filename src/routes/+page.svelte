<script lang="ts">
	import cool from '../lib/images/cool.png';
	import { RadioGroup, RadioItem, AppBar, Avatar } from '@skeletonlabs/skeleton';

	let value: number = 2;

	let touches = new Map();

	function handleTouchStart(event: TouchEvent) {
		event.preventDefault();
		console.log('touchstart', event);
		for (let touch of event.changedTouches) {
			touches.set(touch.identifier, touch);
		}
	}

	function handleTouchMove(event: TouchEvent) {
		event.preventDefault();
		console.log('touchmove', event);
		for (let touch of event.changedTouches) {
			touches.set(touch.identifier, touch);
		}
	}

	function handleTouchEnd(event: TouchEvent) {
		event.preventDefault();
		console.log('touchend', event);
		for (let touch of event.changedTouches) {
			touches.delete(touch.identifier);
		}
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
		on:touchstart={handleTouchStart}
		on:touchmove={handleTouchMove}
		on:touchend={handleTouchEnd}
		style="width: 600px; height: 600px; border: 1px solid black"
	>
		{#each Array.from(touches.values()) as touch}
			<div
				style="position: absolute; left: {touch.clientX}px; top: {touch.clientY}px; width: 50px; height: 50px; background-color: #ff0000; border-radius: 50%;"
			/>
		{/each}
	</div>
</section>
