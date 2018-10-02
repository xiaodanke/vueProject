
<template>
    <div class="shopCart">
        <div class="content">
            <div class="content-left">
                <div class="logo-wrapper">
                    <div class="logo" :class="{'highLight': totalCount > 0}">
                         <i class="icon-shopping_cart" :class="{'highLight': totalCount > 0}"></i>
                    </div>
                    <div class="num" v-show="totalCount > 0">{{totalCount}}</div>
                </div>
                <div class="price" :class="{'highLight': totalCount > 0}">￥{{totalPrice}}</div>
                <div class="des">另需配送费￥{{deliveryPrice}} 元</div>
            </div>
            <div class="content-right">
                <div class="pay" :class="payClass">{{payDesc}}</div>
            </div>
        </div>
    </div>
</template>
<script type="text/ecmascript-6">
export default {
    props: {
        selectFoods: {
            type: Array,
            default() {
                return [
                 {
                     price: 10,
                     count: 0
                 }
                ]
            }
        },
        deliveryPrice: {
            type: Number,
            default: 0
        },
        minPrice: {
            type: Number,
            default: 0
        }
    },
    computed: {
        totalPrice() {
            let total = 0;
            this.selectFoods.forEach((food) => {
                total += food.price * food.count;
            })
            return total;
        },
        totalCount() {
            let count = 0;
            this.selectFoods.forEach((food) => {
                count += food.count;
            })
            return count;
        },
        payDesc() {
            if(this.totalPrice === 0 ){
                return `${this.minPrice}元起送`;
            }else if(this.totalPrice < this.minPrice){
                let diff = this.minPrice - this.totalPrice;
                return `还差${diff}元起送`;
            }else {
                return '去结算';
            }
        },
        payClass() {
            if(this.totalPrice < this.minPrice){
                return 'not-ethough';
            }else{
                return 'ethough';
            }
        }
    }
}
</script>
<style lang="stylus" rel="stylesheet/stylus">
    .shopCart
     position: fixed
     left: 0
     bottom: 0
     width: 100%
     height: 48px
     .content
      display: flex
      background: #141d27
      color: rgba(255, 255, 255, 0.4)
      font-size: 0
      .content-left
        flex: 1
        .logo-wrapper
         display: inline-block
         width: 56px
         height: 56px
         border-radius: 50%
         background: #141d27
         top: -10px
         position: relative
         margin: 0 12px
         padding: 6px
         box-sizing: border-box
         .logo
          width: 100%
          height: 100%
          background: #2b343c
          border-radius: 50%
          text-align: center
          &.highLight
            background: rgb(0,160,220)
          .icon-shopping_cart
           font-size: 24px
           line-height: 44px
           color: #80858a
           &.highLight
            color:#fff
         .num
          position: absolute;
          right: 0
          top: 0
          width: 24px
          height: 16px
          background: #f01414
          color: #ffffff
          line-height: 16px
          text-align: center
          border-radius: 16px
          font-size: 9px
        .price
         display: inline-block
         vertical-align: top
         margin-top: 12px
         line-height: 24px
         font-weight: 700
         font-size: 16px
         border-right: 1px solid rgba(255,255,255,0.1)
         padding-right: 12px
         &.highLight
            color:#fff
        .des
         display: inline-block
         font-size: 10px
         line-height: 24px
         margin: 12px 0 0 12px
         vertical-align: top
      .content-right
        flex: 0 0 105px
        width: 105px
        .pay
         height: 48px
         line-height: 48px
         font-weight: 700
         font-size: 12px
         text-align: center
         &.not-ethough
          background: #2b333b
         &.ethough
          color: #fff
          background: #00b43c
</style>




