<script>
    import { Canvas } from 'svelte-canvas';
    import Ball from '$lib/components/Ball.svelte';

    let balls = [
        { color: 'tomato', x: 0.5, y: 0.333 },
        { color: 'goldenrod', x: 0.333, y: 0.625 },
        { color: 'mediumturquoise', x: 0.667, y: 0.625 }
    ];

    const reorder = (color) => {
        balls = balls
            .filter((c) => c.color !== color)
            .concat(balls.filter((c) => c.color === color));
    };
</script>

<h1 class="content-around text-3xl">Playing with the canvas lib</h1>

<div>
    <div class="canvas">
        <Canvas layerEvents style="touch-action: none">
            {#each balls as { color, x, y } (color)}
                <Ball {color} {x} {y} onclick={() => reorder(color)} />
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
