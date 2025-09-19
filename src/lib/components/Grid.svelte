<script lang="ts">
    import { Layer, type Render } from 'svelte-canvas';

    const RADIUS = 1;

    let { color, xDotsCount, yDotsCount } = $props();

    function drawDot(context: CanvasRenderingContext2D, x: number, y: number) {
        context.lineWidth = 0;
        context.fillStyle = color;
        context.beginPath();
        context.arc(x, y, RADIUS, 0, Math.PI * 2);
        context.fill();
    }

    const render: Render = ({ context, width, height }) => {
        const widthBetweenDots = width / xDotsCount;
        const heightBetweenDots = height / yDotsCount;

        for (let i = 0; i < width; i += widthBetweenDots) {
            for (let j = 0; j < height; j += heightBetweenDots) {
                const i_o = i;
                const j_o = j;
                drawDot(context, i_o, j_o);
            }
        }
    };
</script>

<Layer {render} />
