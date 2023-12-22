<script lang="ts">
	import cool from '../lib/images/cool.png';
	import { RadioGroup, RadioItem, AppBar, Avatar } from '@skeletonlabs/skeleton';

	let value: number = 2;
	let element: HTMLElement;
	let capturedPointerId: number | null = null;
	let pointers = new Set<{ id: number; x: number; y: number; color: string }>();
	let x = 0;
	let y = 0;
	const colors = ['red', 'blue', 'green', 'yellow', 'purple', 'orange', 'pink'];
	let selectedPointer: any = null;
	let timer: number | null = null;

	$: {
		if (pointers.size === value) {
			if (timer) {
				clearTimeout(timer);
			}

			timer = setTimeout(() => {
				const pointersArray = Array.from(pointers);
				selectedPointer = pointersArray[Math.floor(Math.random() * pointersArray.length)];
			}, 2000);
		}
	}

	import { onMount, onDestroy } from 'svelte';

	let isBrowser = typeof window !== 'undefined';

	if (isBrowser) {
		onMount(() => {
			window.addEventListener('pointerup', onPointerUp);
			window.addEventListener('pointerout', onPointerUp);
		});

		onDestroy(() => {
			window.removeEventListener('pointerup', onPointerUp);
			window.removeEventListener('pointerout', onPointerUp);
		});
	}

	function onPointerDown(e: PointerEvent) {
		element.setPointerCapture(e.pointerId);
		capturedPointerId = e.pointerId;
		const rect = element.getBoundingClientRect();
		const color = colors[e.pointerId % colors.length];
		pointers = new Set([
			...pointers,
			{ id: e.pointerId, x: e.clientX - rect.left - 75, y: e.clientY - rect.top - 75, color }
		]);
		e.preventDefault();
	}

	function onPointerUp(e: PointerEvent) {
		capturedPointerId = null;
		pointers = new Set([...pointers].filter((pointer) => pointer.id !== e.pointerId));
		element.releasePointerCapture(e.pointerId);
	}

	function onPointerMove(e: PointerEvent) {
		if (capturedPointerId != e.pointerId) return;

		if ([...pointers].some((pointer) => pointer.id === e.pointerId)) {
			const rect = element.getBoundingClientRect();
			const color = colors[e.pointerId % colors.length];
			pointers = new Set(
				[...pointers].map((pointer) =>
					pointer.id === e.pointerId
						? {
								id: e.pointerId,
								x: e.clientX - rect.left - 75,
								y: e.clientY - rect.top - 75,
								color
						  }
						: pointer
				)
			);
		}

		e.preventDefault();
		e.stopPropagation();

		x += e.movementX;
		y += e.movementY;
	}
</script>

<svelte:head>
	<title>choosy</title>
</svelte:head>

<section>
	<AppBar gridColumns="grid-cols-3" slotDefault="place-self-center" slotTrail="place-content-end">
		<svelte:fragment slot="lead">
			<RadioGroup>
				{#each { length: 4 } as _, i}
					<RadioItem bind:group={value} name="amount-participants" value={i + 2}>{i + 2}</RadioItem>
				{/each}
			</RadioGroup>
		</svelte:fragment>

		<svelte:fragment slot="trail">
			<Avatar src={cool} class="w-11" />
			<h2 class="h3">choosy</h2>
		</svelte:fragment>
	</AppBar>

	<div
		bind:this={element}
		on:pointerdown={onPointerDown}
		on:pointermove={onPointerMove}
		class="w-full h-full absolute inset-0 mt-24"
	>
		<p>pointers={Array.from(pointers).length}</p>
		<p>amount={value}</p>
		<p>selectedPointer={selectedPointer}</p>
		<p>timer={timer}</p>
		{#each Array.from(pointers) as pointer (pointer.id)}
			<div
				style="position: absolute; left: {pointer.x}px; top: {pointer.y}px; width: 150px; height: 150px; background-color: {pointer ===
				selectedPointer
					? 'black'
					: pointer.color}; border-radius: 50%; z-index: 9999;"
			/>
		{/each}
	</div>
</section>
