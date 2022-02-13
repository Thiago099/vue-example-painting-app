<template>
  <div class="container" style="margin-top: 50px">
    <div class="row">
      <canvas 
          class="col-10"
          ref='canvas' 
          height="800" 
          style="border:1px solid #d3d3d3;padding:0px"
          @mousedown.prevent="mousedown = true; mouse=null"
          @mouseup="mousedown = false"
          @mousemove="draw"
          >
      Your browser does not support the HTML canvas tag.</canvas>
      <div class="form-group col-2">
        <label> Color:</label>
        <input class="col-12 form-control" type="color" v-model="color" @change="updateColor($event.target.value)">
        <label> Size:</label>
        <input class="col-12" type="range" min="1" max="300" value="50" v-model="size">
        <button class="form-control" @click="fill">Fill</button>
        <div class="row color-container">
         <div 
          v-for="(item, index) in color_history" 
          :key="item" 
          class="color-box col-3"
          :style="`background-color:${item};${item == color? 'border-color:red':''}`"
          @click.left="updateColor(item)"
          @click.prevent.right="deleteColor(index)"
          >
        </div>
        </div>
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
      opacity:1,
      color:'#000000',
      size:50,
      color_history : [],
    }
  },
  mounted () {
      this.canvas = this.$refs.canvas
      this.ctx = this.canvas.getContext("2d");

      this.resizeCanvas()
      window.onresize = this.resizeCanvas;
  },
  methods:{
    resizeCanvas()
    {
      this.canvas.width = this.canvas.clientWidth
    },
    draw (event) {
      if(this.mousedown)
      {
        this.previous_mouse = this.mouse;
        this.mouse = this.getMousePos(event)
        if(this.previous_mouse == null)
        return;
        // set red background
        this.ctx.beginPath();
        this.ctx.strokeStyle = this.color;
        this.ctx.lineWidth = this.size;
        this.ctx.lineJoin = "round";
        this.ctx.moveTo(this.previous_mouse.x, this.previous_mouse.y);
        this.ctx.lineTo(this.mouse.x, this.mouse.y);
        this.ctx.closePath();
        this.ctx.stroke();
      }
    },
    getMousePos(event) 
    {
      var rect = this.canvas.getBoundingClientRect();
      return {
        x: event.clientX - rect.left,
        y: event.clientY - rect.top
      };
    },
    updateColor(value)
    {
      if(this.color_history.indexOf(value) == -1)
      {
        this.color_history.push(value)
      }
      this.color = value
    },
    deleteColor(index)
    {
      this.color_history.splice(index,1)
    },
    fill(){
      this.ctx.fillStyle = this.color;
      this.ctx.fillRect(0, 0, this.canvas.width, this.canvas.height);
    }
  },
}
</script>

<style>
.color-box{
  height:30px;
  width:30px;
  cursor:pointer;
  margin: 3px;
  border: 1px solid black;
  border-radius: 3px;
}
.color-box-active{
  border: 1px solid v-bind(color);
}
.color-container{
  padding:10px;
}
</style>
