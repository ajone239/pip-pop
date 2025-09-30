<script lang="ts">
    import type { LayerEvent, Render } from 'svelte-canvas';
    import { Layer } from 'svelte-canvas';

    const TAP_TIME = 200;
    const COOLDOWN_TIME = 250;

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
    let debounce = true;
    let downTime = new Date().getTime();
    let rotation = 0;

    let startedDragging = $state(false);
    let dragging = $state(false);
    let inside = $state(false);

    // TODO(austin.jones): the `startDragging` color is for debug take it out
    let stroke = $derived(
        startedDragging ? 'green' : dragging ? 'white' : inside ? 'grey' : 'black'
    );

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

        const x = x_curr - (startedDragging ? boxWidth / 2 : 0);
        const y = y_curr - (startedDragging ? boxHeight / 2 : 0);

        context.translate(x + boxWidth / 2, y + boxHeight / 2);
        context.rotate((rotation * Math.PI) / 2);

        context.roundRect((-1 * boxWidth) / 2, (-1 * boxHeight) / 2, boxWidth, boxHeight, 5);

        context.fill();
        context.stroke();

        context.fillStyle = 'black';

        context.beginPath();
        context.rect(
            (-1 * boxWidth) / 3,
            (-1 * boxHeight) / 64,
            (boxWidth * 2) / 3,
            boxHeight / 32
        );
        context.fill();

        context.beginPath();
        context.arc(0, (-1 * boxHeight) / 4, boxWidth / 10, 0, Math.PI * 2);
        context.fill();

        context.restore();
    };

    const renderShadow: Render = ({ context }) => {
        if (!dragging || x_curr > gridWidth || y_curr + boxHeight > gridWidth) return;

        context.save();

        context.fillStyle = 'gray';
        context.strokeStyle = 'transparent';

        const x = snapToGrid(x_curr, boxWidth / 2);
        const y = snapToGrid(y_curr, boxHeight / 4);

        context.translate(x + boxWidth / 2, y + boxHeight / 2);
        context.rotate((rotation * Math.PI) / 2);

        context.beginPath();
        context.roundRect((-1 * boxWidth) / 2, (-1 * boxHeight) / 2, boxWidth, boxHeight, 5);
        context.fill();
        context.stroke();

        context.restore();
    };

    const render: Render = (props) => {
        renderShadow(props);
        renderMain(props);

        const upTime = new Date().getTime();
        if (upTime - downTime > COOLDOWN_TIME) {
            debounce = true;
        }
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

        if (debounce) {
            downTime = new Date().getTime();
        }
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
        if (upTime - downTime < TAP_TIME && debounce && !startedDragging) {
            rotation += 1;
            debounce = false;
        }

        dragging = false;
        startedDragging = false;
    };

    const onMove = ({ x, y }: LayerEvent) => {
        if (dragging) {
            startedDragging = true;
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
