<template>
    <div class="bottom-bar">
        <div class="check-content">
            <check-button 
                class="check-button" 
                :is-checked="isSelectAll" 
                @click.native="checkClick"/>
            <span>全选</span>
        </div>

        <div class="price">
            合计： {{ totalPrice }}
        </div>

        <div class="calculate" @click="calcClick">
            去结算({{ checkLength }})
        </div>
    </div>
</template>

<script>
    import CheckButton from 'components/content/checkButton/CheckButton'

export default {
    name: "CartBottomBar",
    components: {
        CheckButton
    },
    computed: {
        totalPrice() {
            return '￥' + this.$store.getters.cartList.filter(item => {
                return item.checked
            }).reduce((preValue, item) => {
                return preValue + item.price * item.count
            }, 0).toFixed(2)
        },
        checkLength() {
            return this.$store.state.cartList.filter(item => item.checked).length
        },
        isSelectAll() {
           // return !(this.$store.getters.cartList.filter(item => !item.checked).length)
           if (this.$store.state.cartList.length === 0) return false
           return !this.$store.state.cartList.find(item => !item.checked)
        }
    },
    methods: {
        checkClick() {
            if (this.isSelectAll) {
                //全部选中
                this.$store.getters.cartList.forEach(item => item.checked = false)
            } else {
                this.$store.getters.cartList.forEach(item => item.checked = true)
            }

            // this.$store.getters.cartList.forEach(item => item.checked = !this.isSelectAll)  错误。不能简化
        },
        calcClick() {
            if (!this.isSelectAll) {
                this.$toast.show('请选择购买的商品', 2000)
            }
        }
    }
}
</script>

<style scoped>
    * {color: #777}
    .bottom-bar {
        width: 100%;
        height: 40px;
        background-color: #eee;
        position: absolute;
        left: 0;
        bottom: 49px;

        line-height: 40px;
        display: flex;
        font-size: 14px;
    }

    .check-content {
        display: flex;
        align-items: center;
        margin-left: 10px;
        margin-right: 20px;
        width: 60px;
    }

    .price {
        flex: 1;
    }

    .check-button {
        width: 22px;
        height: 22px;
        line-height: 22px;
        margin-right: 5px;
    }

    .calculate {
        width: 90px;
        color: #fff;
        text-align: center;
        background-color: orangered;
    }
</style>