<template>
  <div class="cart-list">
     <scroll class="content" ref="scroll">
         <cart-list-item 
         v-for="(item, index) in cartList" 
         :item-info='item' 
         :key="index"/>
     </scroll>
  </div>
</template>

<script>
    import Scroll from 'components/common/scroll/Scroll';
    import CartListItem from './CartListItem'

    import { mapGetters } from "vuex";

export default {
  name: "CartList",
  components: {
      Scroll,
      CartListItem
  },
  computed: {
      ...mapGetters(['cartList'])
  },
  activated() {
      this.$refs.scroll.refresh()
  }
};
</script>

<style scoped>
    * {
        color: #333;
    }

    .cart-list {
        height: (100% - 40px -40px);
    }

    .content {
        height: calc(100% - 44px );
        
    }
</style>