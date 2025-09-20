<script lang="ts">
    import { Layer, type Render } from 'svelte-canvas';

    const RADIUS = 1;

    let { color, bgColor, xDotsCount, yDotsCount } = $props();

    function drawDot(context: CanvasRenderingContext2D, x: number, y: number) {
        context.lineWidth = 0;
        context.fillStyle = color;
        context.beginPath();
        context.arc(x, y, RADIUS, 0, Math.PI * 2);
        context.fill();
    }

    function drawBorder(context: CanvasRenderingContext2D, sideLength: number) {
        context.strokeStyle = color;
        context.lineWidth = 2;
        context.fillStyle = bgColor;
        context.beginPath();
        context.roundRect(0, 0, sideLength, sideLength, 5);
        context.stroke();
        context.fill();
    }

    function drawBank(context: CanvasRenderingContext2D, sideLength: number) {
        const initBoxes = [
            { x: 0.2, y: 1.05 },
            { x: 0.45, y: 1.05 },
            { x: 0.7, y: 1.05 }
        ];

        const widthBetweenDots = sideLength / xDotsCount;
        const heightBetweenDots = sideLength / yDotsCount;

        for (let box of initBoxes) {
            context.strokeStyle = color;
            context.lineWidth = 2;
            context.fillStyle = bgColor;
            context.beginPath();

            context.roundRect(
                sideLength * box.x,
                sideLength * box.y,
                widthBetweenDots * 2,
                heightBetweenDots * 4,
                5
            );

            context.stroke();
            context.fill();
        }
    }

    const render: Render = ({ context, width }) => {
        drawBorder(context, width);
        drawBank(context, width);

        const widthBetweenDots = width / xDotsCount;
        const heightBetweenDots = width / yDotsCount;

        for (let i = 0; i < width; i += widthBetweenDots) {
            for (let j = 0; j < width; j += heightBetweenDots) {
                const i_o = i;
                const j_o = j;
                drawDot(context, i_o, j_o);
            }
        }
    };
</script>

<Layer {render} />
