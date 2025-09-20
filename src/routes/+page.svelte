<script lang="ts">
    import { Canvas } from 'svelte-canvas';
    import Grid from '$lib/components/Grid.svelte';
    import Box from '$lib/components/Box.svelte';

    const xDotsCount = 20;

    let boxes = $state([
        { color: 'goldenrod', x: 0.2, y: 1.05 },
        { color: 'tomato', x: 0.45, y: 1.05 },
        { color: 'turquoise', x: 0.7, y: 1.05 }
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

    const addNewBoxes = () => {
        boxes.push(
            ...[
                { color: 'yellow', x: 0.2, y: 1.05 },
                { color: 'blue', x: 0.45, y: 1.05 },
                { color: 'red', x: 0.7, y: 1.05 }
            ]
        );
    };

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
    <button
        class="rounded-sm border-2 border-black bg-teal-200 p-2 text-black hover:bg-teal-300"
        onclick={addNewBoxes}
    >
        Add new boxes
    </button>
</div>

<div class="no-select">
    <div class="canvas">
        <Canvas layerEvents style="touch-action: none">
            <Grid {xDotsCount} yDotsCount={xDotsCount} bgColor="#555" color="gray" />
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
        justify-self: center;
        border-radius: 0.5rem;
    }
    .canvas {
        width: 97%;
        aspect-ratio: 0.75;
        justify-self: center;
        border-radius: 0.5rem;
        border-style: solid;
        border-width: 3px;
        border-color: var(--main-border-color);
        overflow: hidden;
    }
    .no-select {
        -webkit-touch-callout: none; /* iOS Safari */
        -webkit-user-select: none; /* Safari */
        -khtml-user-select: none; /* Konqueror HTML */
        -moz-user-select: none; /* Old versions of Firefox */
        -ms-user-select: none; /* Internet Explorer/Edge */
        user-select: none; /* Non-prefixed version, currently
                                                                                                          supported by Chrome, Edge, Opera and Firefox */
    }
</style>
