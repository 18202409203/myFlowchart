<template>
  <div class="left-area">
    <div class="item">
      <div class="style_1 center_1" ref="style_1"></div>
    </div>
    <div class="item">
      <div class="style_2 center_2" ref="style_2"></div>
    </div>
    <div class="item">
      <div class="style_3 center_3" ref="style_3"></div>
    </div>
    <div class="item">
      <div class="style_4 center_4" ref="style_4"></div>
    </div>
  </div>
</template>

<script>
  export default {
    name: 'LeftArea',
    props: {
      jsp: {
        type: Object
      }
    },
    data () {
      return {
      }
    },
    methods: {
      init () {
        this.jsp.draggable([this.$refs.style_1, this.$refs.style_2, this.$refs.style_3, this.$refs.style_4], {
          clone: true,
          drag: params => {
            this.$emit('update-pos', params.pos)
          }
        })
      }
    },
    mounted () {
      this.init()
    }
  }
</script>

<style lang="css">
  .left-area > .item {
    position: relative;
    width: 100%;
    height: 150px;
    /*border-bottom: 1px solid #333;*/
  }

  .center_1 {
    position: absolute;
    top: calc(50% - 15px); /* calc(50% - 50px); */
    left: calc(50% - 40px); /* calc(50% - 50px); */
  }
  .center_2 {
    position: absolute;
    top: calc(50% - 30px); /* calc(50% - 50px); */
    left: calc(50% - 25px); /* calc(50% - 50px); */
  }
  .center_3 {
    position: absolute;
    top: calc(50% - 30px); /* calc(50% - 50px); */
    left: calc(50% - 40px); /* calc(50% - 50px); */
  }
  .center_4 {
    position: absolute;
    top: calc(50% - 40px); /* calc(50% - 50px); */
    left: calc(50% - 40px); /* calc(50% - 50px); */
  }
</style>
