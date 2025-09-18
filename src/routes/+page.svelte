<script lang="ts">
    import { Canvas } from 'svelte-canvas';
    import Grid from '$lib/components/Grid.svelte';
    import Box from '$lib/components/Box.svelte';

    const grid_width = 40;

    let boxes = $state([
        { color: 'tomato', x: 0.5, y: 0.333 },
        { color: 'goldenrod', x: 0.333, y: 0.625 },
        { color: 'turquoise', x: 0.667, y: 0.625 }
    ]);

    let header = $derived.by(() => {
        const xs = boxes.map((b) => b.x);
        const ys = boxes.map((b) => b.y);

        if (xs.every((x) => x == xs[0])) {
            return 'Xs Match!';
        } else if (ys.every((y) => y == ys[0])) {
            return 'ys Match!';
        } else {
            return 'Playing with the canvas lib';
        }
    });

    $inspect(boxes);
</script>

<h1 class="content-around text-3xl">{header}</h1>

<div>
    <ul>
        {#each boxes as { color, x, y } (color)}
            <li>
                <p>
                    <b>{color}</b>: {x}, {y}
                </p>
            </li>
        {:else}
            <li><p>Sry no boxes</p></li>
        {/each}
    </ul>
</div>

<div>
    <div class="canvas">
        <Canvas layerEvents style="touch-action: none">
            <Grid {grid_width} color="grey" />
            {#each boxes as box, i}
                <Box
                    color={box.color}
                    {grid_width}
                    bind:x_curr={boxes[i].x}
                    bind:y_curr={boxes[i].y}
                    onclick={() => {}}
                />
            {/each}
        </Canvas>
    </div>
</div>

<style lang="scss">
    div {
        margin: 20;
        justify-self: center;
        border-radius: 0.5rem;
    }
    .canvas {
        width: 100%;
        aspect-ratio: 1;
        justify-self: center;
        border-radius: 0.5rem;
        overflow: hidden;
        background-color: var(--bg2-color);
    }
</style>
