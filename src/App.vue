<script>
export default {
    data() {
        return {
            context: null,
            draw: false,
            color: 0,
            lastX: null,
            lastY: null,
            lineWidthState: false
        };
    },
    computed: {
        hslColor() {
            return `hsl(${this.color}, 100%, 50%)`;
        }
    },
    mounted() {
        this.context = this.$refs.canvas.getContext('2d');
        
        this.setDefault();

        window.addEventListener('resize', this.setDefault);
    },
    methods: {
        setDefault() {
            this.$refs.canvas.height = window.innerHeight;
            this.$refs.canvas.width = window.innerWidth;
            this.context.lineWidth = 10;
            this.context.lineCap = 'round';
            this.context.lineJoin = 'round';
        },
        drawLine({ offsetX, offsetY }) {
            this.context.beginPath();
            this.context.strokeStyle = this.hslColor;

            this.color++;
            if(this.color === 360)
            {
                this.color = 0;
            }

            (this.lineWidthState)? this.context.lineWidth-- : this.context.lineWidth++;

            this.context.moveTo(this.lastX, this.lastY);
            this.context.lineTo(offsetX, offsetY);  
            this.context.stroke();

            this.lastX = offsetX;
            this.lastY = offsetY;

            if(this.context.lineWidth >= 100)
            {
                this.lineWidthState = true;
            }
            else if(this.context.lineWidth <= 10)
            {
                this.lineWidthState = false;
            }
        },  
        mouseDownHandler(event) {
            this.lastX = event.offsetX;
            this.lastY = event.offsetY;

            this.drawLine(event);

            this.draw = true;
        },  
        mouseUpHandler() {
            this.draw = false;
        },  
        mouseMove(event) {
            if(this.draw) 
            {
                this.drawLine(event);
            }
        }
    }
};
</script>

<template>
    <canvas ref="canvas" class="draw" @mousemove="mouseMove" @mousedown="mouseDownHandler" @mouseup="mouseUpHandler"></canvas>
</template>

<style scoped>
    .draw {
        position: fixed;
        top: 0;
        left: 0;
        min-height: 100vh;
    }
</style>