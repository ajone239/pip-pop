<script lang="ts">
	import { useDraggable } from '@dnd-kit-svelte/core';
	import { CSS } from '@dnd-kit-svelte/utilities';

	const { transform, listeners, attributes, node } = useDraggable({ id: 'draggable' });

	const style = $derived(
		transform.current ? `transform: ${CSS.Translate.toString(transform.current)};` : ''
	);
</script>

<div
	class="inline-flex rounded-lg bg-orange-500 p-4 font-bold text-white"
	{style}
	bind:this={node.current}
	{...listeners.current}
	{...attributes.current}
>
	Drag me
</div>
