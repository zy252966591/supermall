<template>
  <div id="detail">
    <detail-nav-bar class="detail-nav" @titleClick="titleClick" ref="nav"></detail-nav-bar>
    <scroll class="content" ref="scroll" :probe-type="3" @scroll="contentScroll">
      <detail-swiper :top-images="topImages" />
      <detail-base-info :goods="goods" />
      <detail-shop-info :shop="shop" />
      <detail-goods-info :detail-info="detailInfo" @imageLoad="imageLoad" />
      <detail-param-info ref="params" :param-info="paramInfo" />
      <detail-comment-info ref="comment" :comment-info="commentInfo" />
      <goods-list ref="recommend" :goods = "recommends"></goods-list>
    </scroll>
    <detail-bottom-bar @addCart="addToCart"/>
    <back-top @click.native="backClick" v-show="isShowBackTop" />
  </div>
</template>

<script>
import DetailNavBar from "./childComps/DetailNavBar";
import DetailSwiper from "./childComps/DetailSwiper";
import DetailBaseInfo from "./childComps/DetailBaseInfo";
import DetailShopInfo from "./childComps/DetailShopInfo";
import DetailGoodsInfo from "./childComps/DetailGoodsInfo";
import DetailParamInfo from "./childComps/DetailParamInfo";
import DetailCommentInfo from "./childComps/DetailCommentInfo";
import DetailBottomBar from "./childComps/DetailBottomBar";

import Scroll from "components/common/scroll/Scroll";
import GoodsList from "components/content/goods/GoodsList";

import { getDetail, Goods, Shop ,GoodsParam, getRecommend} from "network/detail";
import {debouce} from "common/utils";
import {itemListenerMixin, backTopMixin} from "common/mixin";
import { nextTick } from 'q';

export default {
  name: "Detail",
  components: {
    DetailNavBar,
    DetailSwiper,
    DetailBaseInfo,
    DetailShopInfo,
    DetailGoodsInfo,
    DetailParamInfo,
    DetailCommentInfo,
    DetailBottomBar,

    Scroll,
    GoodsList,
  },
  mixins:[itemListenerMixin,backTopMixin],
  data() {
    return {
      iid: null,
      topImages: [],
      goods: {},
      shop: {},
      detailInfo: {},
      paramInfo: {},
      commentInfo: {},
      recommends: [],
      // goodsList: [],
      themeTopYs: [],
      getThemeTopY: null,
      currentIndex: 0,
    };
  },
  created() {
    //1.保存传入的id
    this.iid = this.$route.params.iid;

    //2.根据iid请求详情数据
    getDetail(this.iid).then(res => {
      //1.获取顶部的图片轮播数据
      // console.log(res);
      const data = res.result;
      this.topImages = data.itemInfo.topImages;
      //2.获取商品信息
      this.goods = new Goods(
        data.itemInfo,
        data.columns,
        data.shopInfo.services
      );

      //3.创建店铺信息的对象
      this.shop = new Shop(data.shopInfo);

      //4. 保存商品详情数据
      this.detailInfo = data.detailInfo;

      //5. 获取参数信息
      this.paramInfo = new GoodsParam(data.itemParams.info, data.itemParams.rule);

      //6. 获取评论信息
      if (data.rate.cRate !== 0) {
        this.commentInfo = data.rate.list[0]
      }

      // this.$nextTick(()=>{
      //   this.themeTopYs = [];
      //   this.themeTopYs.push(0);
      //   this.themeTopYs.push(this.$refs.params.$el.offsetTop - 49);
      //   this.themeTopYs.push(this.$refs.comment.$el.offsetTop - 49);
      //   this.themeTopYs.push(this.$refs.recommend.$el.offsetTop - 49);
      // })
      
    });

    //3.请求推荐的数据
    getRecommend().then(res => {
      this.recommends = res.data.list;
    })

    //4.给getThemeTopY赋值
    this.getThemeTopY = debouce(() => {
        this.themeTopYs = [];
        this.themeTopYs.push(0);
        this.themeTopYs.push(this.$refs.params.$el.offsetTop - 49);
        this.themeTopYs.push(this.$refs.comment.$el.offsetTop - 49);
        this.themeTopYs.push(this.$refs.recommend.$el.offsetTop - 49);
        this.themeTopYs.push(Number.MAX_VALUE);
    },100);

    
  },
  mounted() {

  },
  updated() {
      // this.themeTopYs = [];
      // this.themeTopYs.push(0);
      // this.themeTopYs.push(this.$refs.params.$el.offsetTop);
      // this.themeTopYs.push(this.$refs.comment.$el.offsetTop);
      // this.themeTopYs.push(this.$refs.recommend.$el.offsetTop);
  },
  destroyed() {
    this.$bus.$off('itemImgLoad', this.itemImgListener ) 
  },
  methods: {
    imageLoad() {
      this.newRefresh();
      this.getThemeTopY();
    },
    titleClick(index) {
      // console.log(index);
      this.$refs.scroll.scrollTo(0,-this.themeTopYs[index],200)
    },
    contentScroll(position) {
      //1.获取y值
      const positionY = -position.y;
      //2.positionY和主题中的值进行对比
      let length = this.themeTopYs.length;
      // console.log(this.themeTopYs[0]);
      // console.log(this.themeTopYs[1]);
      // console.log(this.themeTopYs[2]);
      // console.log(this.themeTopYs[3]);
      // console.log(positionY)
      for(let i = 0; i < length-1 ; i++){
        // if(this.currentIndex !== i && ((i < length - 1 && positionY >= this.themeTopYs[i] && positionY < this.themeTopYs[i+1]) || (i === length - 1 && positionY >= this.themeTopYs[i]))) {
        //   this.currentIndex = i;
        //   this.$refs.nav.currentIndex = this.currentIndex;
        // }
        if (this.currentIndex !== i && (positionY >= this.themeTopYs[i] && positionY < this.themeTopYs[i+1] )){
          this.currentIndex = i;
          this.$refs.nav.currentIndex = this.currentIndex;
        } 
      }
      //3. 判断BackTop是否显示
      this.isShowBackTop =  (-position.y) > 1000
    },
    addToCart() {
      //1.获取购物车需要展示的商品信息
      const product = {};
      product.image = this.topImages[0];
      product.title = this.goodsInfo.title;
      product.desc = this.goodsInfo.desc;
      product.price = this.goodsInfo.newPrice;
      product.iid = this.iid;
      
      //2.将商品加入购物车里面
      
    }
  },
};
</script>

<style scoped>
#detail {
  position: relative;
  z-index: 9;
  background-color: #fff;
  height: 100vh;
}

.detail-nav {
  position: relative;
  z-index: 9;
  background-color: #fff;
}

.content {
  height: calc(100% - 44px );
}
</style>