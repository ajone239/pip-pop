<script lang="ts">
    import type { LayerEvent, Render } from 'svelte-canvas';
    import { Layer } from 'svelte-canvas';

    const TAP_TIME = 500;

    let {
        x_curr = $bindable(),
        y_curr = $bindable(),
        xDotsCount,
        yDotsCount,
        color,
        onclick
    } = $props();

    let gridWidth: number;
    let boxWidth: number;
    let boxHeight: number;
    let x_last: number;
    let y_last: number;
    let downTime = new Date().getTime();
    let rotation = 0;

    let dragging = $state(false);
    let inside = $state(false);

    let stroke = $derived(dragging ? 'white' : inside ? 'grey' : 'black');

    function snapToGrid(val: number, boxLength: number): number {
        return Math.floor(val / boxLength) * boxLength;
    }

    const setup: Render = ({ width }) => {
        gridWidth = width;
        boxWidth = (2 * width) / xDotsCount;
        boxHeight = (4 * width) / yDotsCount;

        x_curr = width * x_curr;
        y_curr = width * y_curr;
    };

    const renderMain: Render = ({ context }) => {
        context.save();

        context.fillStyle = color;
        context.strokeStyle = stroke;
        context.lineWidth = 2;
        context.beginPath();
        const x = x_curr;
        const y = y_curr;

        context.translate(x, y);
        context.rotate((rotation * Math.PI) / 2);

        context.roundRect(0, 0, boxWidth, boxHeight, 5);
        context.fill();
        context.stroke();

        context.restore();
    };

    const renderShadow: Render = ({ context }) => {
        if (!dragging || x_curr > gridWidth || y_curr + boxHeight > gridWidth) return;

        context.fillStyle = 'gray';
        context.strokeStyle = 'transparent';

        // add the width from the coords back in
        const x = snapToGrid(x_curr + boxWidth / 2, boxWidth / 2);
        const y = snapToGrid(y_curr + boxHeight / 2, boxHeight / 4);

        context.beginPath();
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
        inside = true;
    };

    const onLeave = () => {
        document.body.style.cursor = 'auto';
        dragging = false;
        inside = false;
    };

    const onDown = () => {
        dragging = true;
        x_last = x_curr;
        y_last = y_curr;
        onclick?.();

        downTime = new Date().getTime();
    };

    const onUp = ({ x, y }: LayerEvent) => {
        if (x > gridWidth || y + boxHeight > gridWidth) {
            x_curr = x_last;
            y_curr = y_last;
        } else {
            x_curr = snapToGrid(x, boxWidth / 2);
            y_curr = snapToGrid(y, boxHeight / 4);
        }

        const upTime = new Date().getTime();
        if (upTime - downTime < TAP_TIME) {
            rotation += 1;
        }

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
