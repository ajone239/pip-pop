<script lang="ts">
    import { Layer, type Render } from 'svelte-canvas';

    const RADIUS = 3;

    let { color, grid_width } = $props();

    function drawDot(context: CanvasRenderingContext2D, x: number, y: number) {
        context.lineWidth = 0;
        context.fillStyle = color;
        context.beginPath();
        context.arc(x, y, RADIUS, 0, Math.PI * 2);
        context.fill();
    }

    const render: Render = ({ context, width, height }) => {
        const x_offset = (width % grid_width) / 2;
        const y_offset = (height % grid_width) / 2;
        for (let i = 0; i < width; i += grid_width) {
            for (let j = 0; j < height; j += grid_width) {
                const i_o = i + x_offset;
                const j_o = j + y_offset;
                drawDot(context, i_o, j_o);
            }
        }
    };
</script>

<Layer {render} />
