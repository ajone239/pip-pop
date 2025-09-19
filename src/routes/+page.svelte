<script lang="ts">
    import { Canvas } from 'svelte-canvas';
    import Grid from '$lib/components/Grid.svelte';
    import Box from '$lib/components/Box.svelte';

    const xDotsCount = 20;

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
            return 'Playing with canvas';
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
                    <b>{color}</b>: {Math.floor(x)}, {Math.floor(y)}
                </p>
            </li>
        {:else}
            <li><p>Sry no boxes</p></li>
        {/each}
    </ul>
</div>

<div>
    <div class="canvas noselect">
        <Canvas layerEvents style="touch-action: none">
            <Grid {xDotsCount} yDotsCount={xDotsCount} color="gray" />
            {#each boxes as box, i}
                <Box
                    color={box.color}
                    {xDotsCount}
                    yDotsCount={xDotsCount}
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
        margin: 10;
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
    .noselect {
        -webkit-touch-callout: none; /* iOS Safari */
        -webkit-user-select: none; /* Safari */
        -khtml-user-select: none; /* Konqueror HTML */
        -moz-user-select: none; /* Old versions of Firefox */
        -ms-user-select: none; /* Internet Explorer/Edge */
        user-select: none; /* Non-prefixed version, currently
                                                                                                          supported by Chrome, Edge, Opera and Firefox */
    }
</style>
