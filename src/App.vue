<template>
  <div class="container" style="margin-top: 50px">
    <div class="row">
      <canvas 
          class="col-10"
          ref='canvas' 
          height="800" 
          style="border:1px solid #d3d3d3;"
          @mousedown.prevent="mousedown = true; mouse=null"
          @mouseup="mousedown = false"
          @mousemove="draw"
          >
      Your browser does not support the HTML canvas tag.</canvas>
      <div class="form-group col-2">
        <label> Color:</label>
        <input class="col-12 form-control" type="color" @change="color = $event.target.value">
        <label> Size:</label>
        <input class="col-12 form-control" type="range" min="1" max="100" value="50" @change="size = $event.target.value">
      </div>
    </div>

  </div>
</template>

<script>
export default {
  name: 'app',
  data () {
    return {
      mousedown: false,
      mouse:null,
      previous_mouse:null,
      canvas:null,
      ctx:null,
      color:'#000000',
      size:50
    }
  },
  mounted () {
      this.canvas = this.$refs.canvas
      this.ctx = this.canvas.getContext("2d");

      this.resizeCanvas()
      window.onresize = this.resizeCanvas;
  },
  methods:{
    resizeCanvas(){
      this.canvas.width = this.canvas.clientWidth
    },
    draw (event) {
      if(this.mousedown)
      {
        this.previous_mouse = this.mouse;
        this.mouse = this.getMousePos(event)
        if(this.previous_mouse == null)
        return;
        // ctx.clearRect(0, 0, canvas.width, canvas.height);
        this.ctx.beginPath();
        this.ctx.strokeStyle = this.color;
        this.ctx.lineWidth = this.size;
        this.ctx.lineJoin = "round";
        this.ctx.moveTo(this.previous_mouse.x, this.previous_mouse.y);
        this.ctx.lineTo(this.mouse.x, this.mouse.y);
        this.ctx.closePath();
        this.ctx.stroke();

        // draw line from previous mouse to mouse
      }
    },
    getMousePos(event) {
        var rect = this.canvas.getBoundingClientRect();
        return {
          x: event.clientX - rect.left,
          y: event.clientY - rect.top
        };
      },
    }
  }
</script>

<style>

</style>
