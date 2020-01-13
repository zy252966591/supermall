<template>
  <div class="cart">
    <!-- 导航 -->
    <nav-bar class="nav-bar">
      <div slot="center">购物车({{length}})</div>
    </nav-bar>

    <!-- 商品列表 -->
    <cart-list class="content"/>
    <!-- 底部汇总 -->
  </div>
</template>

<script>
  import NavBar from 'components/common/navbar/NavBar';
  import CartList from './childComps/CartList';

  import { mapGetters } from 'vuex';

  export default {
    name: "Cart",
    components: {
      NavBar,
      CartList
    },
    computed: {
       ...mapGetters({
         length: 'cartLength'
       })
    }
  }
</script>

<style scoped>
  * {
    color: #FFF;
  }

  .cart {
    position: relative;
    height: 100vh;
  }

  .nav-bar {
    background-color: var(--color-tint);
    color: #FFF;
    position: fixed;
    top: 0;
    left: 0;
    right: 0;
    z-index: 99;
  }

  .content {
    overflow: hidden;
  
    position: absolute;
    top: 44px;
    bottom: 49px;
    left: 0;
    right: 0;
  }
</style>
