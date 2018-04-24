<template>
  <div id="app">
    <left-area :jsp="jsp" @update-pos="updatePosition"></left-area>
    <right-area :jsp="jsp" :pos="pos"></right-area>
  </div>
</template>

<script>
  import LeftArea from './components/LeftArea'
  import RightArea from './components/RightArea'

  export default {
    name: 'app',
    components: {
      LeftArea,
      RightArea
    },
    data () {
      return {
        jsp: this.$jsplumb.getInstance(),
        pos: [0, 0]
      }
    },
    methods: {
      updatePosition (pos) {
        this.pos = pos
      }
    }
  }
</script>

<style>
  html, body, #app, .left-area, .right-area {
    position: relative;
    height: 100%;
    -webkit-box-sizing: border-box;
    -moz-box-sizing: border-box;
    box-sizing: border-box;
    font-size: 0;
  }

  .left-area, .right-area {
    display: inline-block;
    border: 1px solid #333;
  }

  .left-area {
    width: 10%;
  }

  .right-area {
    width: 90%;
    /*margin-left: 1%;*/
  }

  /*.square {
    position: absolute;
    width: 100px;
    height: 100px;
    border: 1px solid #333;
    border-radius: 5px;
    box-sizing: border-box;
  }

  .circle {
    position: absolute;
    width: 100px;
    height: 100px;
    border: 1px solid #333;
    border-radius: 50%;
    box-sizing: border-box;
  }

  .rect {
    position: absolute;
    top: 25px;
    width: 100px;
    height: 50px;
    border: 1px solid #333;
    box-sizing: border-box;
  }

  .diamond {
    position: absolute;
    top: 25px;
    width: 100px;
    height: 72px;
    background: #fff url(QQ截图20170609173054.png) 0 0 no-repeat;
    -webkit-background-size: 100px 72px;
    background-size: 100px 72px;
    box-sizing: border-box;
  }*/
  .style_1,.style_2,.style_3,.style_4{
    background-color:white;
    border:1px solid #346789;
    text-align:center;
    z-index:24;
    cursor:pointer;
    box-shadow: 2px 2px 19px #aaa;
    -o-box-shadow: 2px 2px 19px #aaa;
    -webkit-box-shadow: 2px 2px 19px #aaa;
    -moz-box-shadow: 2px 2px 19px #aaa;
    -moz-border-radius:0.5em;
    border-radius:0.5em;
    position:absolute;
    color:black;
    padding:0.5em;
    width:80px;
    height:80px;
    line-height: 80px;
    -webkit-transition: -webkit-box-shadow 0.15s ease-in;
    -moz-transition: -moz-box-shadow 0.15s ease-in;
    -o-transition: -o-box-shadow 0.15s ease-in;
    transition: box-shadow 0.15s ease-in;
  }
  .style_1 {
    position: absolute;
   	width:80px; height:30px;
	  line-height:30px;
    border:1px dotted #234b5e;
    border-radius: 10px;
    box-sizing: border-box;
    font-size:15px;
  }
  .style_2 {
    position: absolute;
   	width:50px; height:60px;
	  line-height:60px;
    border:1px dotted #234b5e;
    border-radius: 10px;
    box-sizing: border-box;
    font-size:15px;
  }
  .style_3 {
    position: absolute;
    width:80px; height:60px;
	  line-height:60px;
    border:1px dotted #234b5e;
    border-radius: 5px;
    box-sizing: border-box;
    font-size:15px;
  }
  .style_4 {
   position: absolute;
   width:80px; height:80px;
	 line-height:60px;
   border:1px dotted #234b5e;   
   box-sizing: border-box;
   font-size:15px;
   border-radius: 50%;
  }

  .jsp-label {
    background-color: white;
    padding: 0.4em;
    font: 12px sans-serif;
    color: #444;
    z-index: 21;
    border: 1px dotted gray;
    opacity: 0.8;
    cursor: pointer;
  }

  .jsp-label.jtk-hover {
    background-color: #5c96bc;
    color: white;
    border: 1px solid white;
  }
</style>
