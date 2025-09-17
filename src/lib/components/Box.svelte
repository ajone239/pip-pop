<script lang="ts">
    import type { LayerEvent, Render } from 'svelte-canvas';
    import { Layer } from 'svelte-canvas';

    let { x_init, y_init, color, onclick } = $props();

    let dragging = $state(false);

    let x_curr = $state(0);
    let y_curr = $state(0);
    let stroke = $state('black');

    let radius = $state(40);

    const setup: Render = ({ width, height }) => {
        const x = width * x_init;
        const y = height * y_init;
        x_curr = Math.floor(x / 40) * 40 + 3;
        y_curr = Math.floor(y / 40) * 40 + 3;
    };

    const render: Render = ({ context }) => {
        context.fillStyle = color;
        context.strokeStyle = stroke;
        context.lineWidth = 5;
        context.beginPath();
        context.rect(x_curr - radius, y_curr - radius, 2 * radius, 2 * radius);
        context.fill();
        context.stroke();
    };

    const onEnter = () => {
        document.body.style.cursor = 'pointer';
        stroke = 'grey';
    };

    const onLeave = () => {
        document.body.style.cursor = 'auto';
        dragging = false;
        stroke = 'black';
    };

    const onDown = (e: LayerEvent) => {
        dragging = true;
        stroke = 'white';
        onclick?.();
    };

    const onUp = ({ x, y }: LayerEvent) => {
        x_curr = Math.floor(x / 40) * 40;
        y_curr = Math.floor(y / 40) * 40;

        stroke = 'black';
        dragging = false;
    };

    const onMove = ({ x, y }: LayerEvent) => {
        if (dragging) {
            x_curr = x;
            y_curr = y;
        }
    };
</script>

<Layer
    {setup}
    {render}
    onmouseenter={onEnter}
    onmouseleave={onLeave}
    onmousedown={onDown}
    onmousemove={onMove}
    onmouseup={onUp}
    ontouchstart={onDown}
    ontouchmove={onMove}
    ontouchend={onUp}
/>
