<script lang="ts">
    import type { LayerEvent, Render } from 'svelte-canvas';
    import { Layer } from 'svelte-canvas';

    let { x_init, y_init, grid_width, color, onclick } = $props();

    const width = grid_width;
    const height = grid_width * 2;

    let dragging = $state(false);

    let x_curr = $state(0);
    let y_curr = $state(0);
    let stroke = $state('black');

    function snapToGrid(val: number): number {
        return Math.floor(val / grid_width) * grid_width + 4;
    }

    const setup: Render = ({ width, height }) => {
        const x = width * x_init;
        const y = height * y_init;
        x_curr = snapToGrid(x);
        y_curr = snapToGrid(y);
    };

    const renderMain: Render = ({ context }) => {
        context.fillStyle = color;
        context.strokeStyle = stroke;
        context.lineWidth = 2;
        context.beginPath();
        // subtract the width from the coords to center the mouse in the object

        const x = x_curr - (dragging ? width / 2 : 0);
        const y = y_curr - (dragging ? height / 2 : 0);

        context.roundRect(x, y, width, height, 5);
        context.fill();
        context.stroke();
    };

    const renderShadow: Render = ({ context }) => {
        if (!dragging) return;

        context.fillStyle = 'gray';
        context.strokeStyle = 'transparent';

        context.beginPath();

        const x = snapToGrid(x_curr);
        const y = snapToGrid(y_curr);

        // subtract the width from the coords to center the mouse in the object
        context.roundRect(x, y, width, height, 5);
        context.fill();
        context.stroke();
    };

    const render: Render = (props) => {
        renderShadow(props);
        renderMain(props);
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

    const onDown = () => {
        dragging = true;
        stroke = 'white';
        onclick?.();
    };

    const onUp = ({ x, y }: LayerEvent) => {
        x_curr = snapToGrid(x);
        y_curr = snapToGrid(y);

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
