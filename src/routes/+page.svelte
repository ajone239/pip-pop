<script lang="ts">
    import { Canvas } from 'svelte-canvas';
    import Grid from '$lib/components/Grid.svelte';
    import Box from '$lib/components/Box.svelte';

    let boxes = [
        { color: 'tomato', x: 0.5, y: 0.333 },
        { color: 'goldenrod', x: 0.333, y: 0.625 },
        { color: 'mediumturquoise', x: 0.667, y: 0.625 }
    ];

    function reorder(color: string) {
        boxes = boxes
            .filter((c) => c.color !== color)
            .concat(boxes.filter((c) => c.color === color));
    }
</script>

<h1 class="content-around text-3xl">Playing with the canvas lib</h1>

<div>
    <div class="canvas">
        <Canvas layerEvents style="touch-action: none">
            <Grid color="grey" />
            {#each boxes as { color, x, y } (color)}
                <Box {color} x_init={x} y_init={y} onclick={() => reorder(color)} />
            {/each}
        </Canvas>
    </div>
</div>

<style lang="scss">
    div {
        width: 100%;
        aspect-ratio: 1;
        justify-self: center;
        border-radius: 0.5rem;
        overflow: hidden;
    }
    .canvas {
        background-color: var(--bg2-color);
    }
</style>
