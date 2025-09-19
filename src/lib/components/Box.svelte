<script lang="ts">
    import type { LayerEvent, Render } from 'svelte-canvas';
    import { Layer } from 'svelte-canvas';

    let {
        x_curr = $bindable(),
        y_curr = $bindable(),
        xDotsCount,
        yDotsCount,
        color,
        onclick
    } = $props();

    let boxWidth: number;
    let boxHeight: number;

    let dragging = $state(false);

    let stroke = $state('black');

    function snapToGrid(val: number, boxLength: number): number {
        return Math.floor(val / boxLength) * boxLength;
    }

    const setup: Render = ({ width, height }) => {
        boxWidth = (2 * width) / xDotsCount;
        boxHeight = (4 * height) / yDotsCount;

        const x = width * x_curr;
        const y = height * y_curr;
        x_curr = snapToGrid(x, boxWidth);
        y_curr = snapToGrid(y, boxHeight);
    };

    const renderMain: Render = ({ context }) => {
        context.fillStyle = color;
        context.strokeStyle = stroke;
        context.lineWidth = 2;
        context.beginPath();
        const x = x_curr;
        const y = y_curr;

        context.roundRect(x, y, boxWidth, boxHeight, 5);
        context.fill();
        context.stroke();
    };

    const renderShadow: Render = ({ context }) => {
        if (!dragging) return;

        context.fillStyle = 'gray';
        context.strokeStyle = 'transparent';

        context.beginPath();

        // add the width from the coords back in
        const x = snapToGrid(x_curr + boxWidth / 2, boxWidth);
        const y = snapToGrid(y_curr + boxHeight / 2, boxHeight / 2);

        context.roundRect(x, y, boxWidth, boxHeight, 5);
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
        x_curr = snapToGrid(x, boxWidth);
        y_curr = snapToGrid(y, boxHeight / 2);

        stroke = 'black';
        dragging = false;
    };

    const onMove = ({ x, y }: LayerEvent) => {
        if (dragging) {
            // subtract the width from the coords to center the mouse in the object
            x_curr = x - boxWidth / 2;
            y_curr = y - boxHeight / 2;
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
