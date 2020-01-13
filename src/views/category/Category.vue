<template>
  <div>
    <h2>分类</h2>
    <back-top @click.native="backTop" v-show="isShowBackTop" />
  </div>
</template>

<script>
  import BScroll from 'better-scroll'
  import {backTopMixin} from "common/mixin"

  export default {
    name: "Category",
    mixins: [backTopMixin],
    
  }
  
</script>
  
<style scoped>
  .wrapper {
    height: 150px;
    background-color: aquamarine;
    overflow: hidden;
    /* overflow-y: 150px; */
  }
</style>
