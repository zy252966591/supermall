<template>
  <div ref="wrapper" class="wrapper">
    <div>
      <slot></slot>
    </div>
  </div>
</template>

<script>
  import BScroll from 'better-scroll'
import { log } from 'util'

  export default {
    name: "Scroll",
    props: {
      probeType: {
        type: Number,
        default: 0
      },
      click: {
        type: Boolean,
        default: true
      },
      pullUpLoad: {
        type: Boolean,
        default: false
      },
      data: {
        type: Array,
        default() {
          return []
        }
      }
    },
    data() {
      return {
        scroll: null
      }
    },
    computed: {
    	scrollY() {
    		return this.scroll.y
      }
    },
    mounted() {
      //1.创建BScroll对象
      // this.scroll = new BScroll(this.$refs.wrapper, {
      //   click: true,
      //   probeType: this.probeType,
      //   pullUpload: this.pullUpLoad
      // })
      //2.监听滚动的位置
      // this.scroll.on('scroll',(position) => {
      //   this.$emit('scroll',position)
      // })
      // 1.创建BetterScroll
        this.scroll = new BScroll(this.$refs.wrapper, {
          probeType: this.probeType,
          click: this.click,
          pullUpLoad: this.pullUpLoad
        })

        // 2.事件滚动
        if (this.probeType === 2 || this.probeType === 3) {
          this.scroll.on('scroll', position => {
            // console.log(position);
            this.$emit('scroll', position)
          })
        }

        // 3.上拉加载
        if (this.pullUpLoad) {
          this.scroll.on('pullingUp', () => {
            console.log('上拉加载更多');
            this.$emit('pullingUp') 
          })
        }
    },
    methods: {
      _initScroll() {
        // 1.创建BetterScroll
        this.scroll = new BScroll(this.$refs.wrapper, {
          probeType: this.probeType,
          click: this.click,
          pullUpLoad: this.pullUpLoad
        })

        // 2.事件滚动
        if (this.probeType === 2 || this.probeType === 3) {
          this.scroll.on('scroll', position => {
            console.log(position);
            this.$emit('scroll', position)
          })
        }

        // 3.上拉加载
        if (this.pullUpLoad) {
          this.scroll.on('pullingUp', () => {
            console.log('上拉加载更多');
            this.$emit('pullingUp') 
          })
        }
      },
      refresh() {
       
        this.scroll && this.scroll.refresh && this.scroll.refresh()
      },
      finishedPullUp() {
        this.scroll && this.scroll.finishPullUp && this.scroll.finishPullUp()
      },
      scrollTo(x, y, time=100) {
        this.scroll && this.scroll.scrollTo && this.scroll.scrollTo(x, y, time)
      },
      getScrollY() {
        return this.scroll ? this.scroll.y : 0
      }
    },
    watch: {
      data() {
	      setTimeout(this.refresh, 20)
      }
    }
  }
</script>

<style scoped>
  .wrapper {
    overflow: hidden;
  }
</style>