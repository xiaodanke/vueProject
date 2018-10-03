
<template>
    <div>
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
     <div class="bail-container">
         <div v-for="(bail,index) in bails" :key="index">
             <transition name="drop" @before-enter="beforeDrop" @enter="dropping" @after-enter="afterDrop">
                 <div class="bail" v-show="bail.show">
                     <div class="inner inner-hook"></div>
                 </div>
             </transition>
         </div>
     </div>
    </div>
    
</template>
<script type="text/ecmascript-6">
export default {
    data() {
        return {
            bails: [
                {
                    show: false
                },
                {
                    show: false
                },
                {
                    show: false
                },
                {
                    show: false
                },
                {
                    show: false
                }
            ],
            dropBails: [],
            fold: true
        }
    },
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
    },
    methods: {
        drop(el) {
            for(let i=0;i < this.bails.length;i++){
                let bail = this.bails[i];
                if(!bail.show){
                    bail.show = true;
                    bail.el = el;
                    this.dropBails.push(bail);
                    return;
                }
            }
        },
        beforeDrop(el) {
            let count = this.bails.length;
            while(count--){
                let bail = this.bails[count];
                if(bail.show){
                    let rect = bail.el.getBoundingClientRect();
                    let x = rect.left - 32;
                    let y = -(window.innerHeight - rect.top - 22);
                    el.style.display = '';
                    el.style.webkitTransform=`translate3d(0,${y}px,0)`;
                    el.style.transform = `translate3d(0,${y}px,0)`;
                    let inner = el.getElementsByClassName('inner-hook')[0];
                    inner.style.webkitTransform = `translate3d(${x}px,0,0)`;
                    inner.style.transform = `translate(${x}px,0,0)`;
                }
            }

        },
        dropping(el) {
            let rf = el.offsetHeight;
            this.$nextTick(() => {
                el.style.webkitTransform = 'translate3d(0,0,0)';
                el.style.transform = 'translate3d(0,0,0)';
                let inner = el.getElementsByClassName('inner-hook')[0];
                inner.style.webkitTransform = 'translate3d(0,0,0)';
                inner.style.transform = 'translate3d(0,0,0)';
            })
        },
        afterDrop(el) {
          let bail = this.dropBails.shift();
          if(bail){
              bail.show = false;
              el.style.display = 'none';
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
    .bail-container
     .bail
       position: fixed
       left: 32px
       bottom: 22px
       z-index: 200
       transition: all 0.4s cubic-bezier(0.49, -0.29, 0.75, 0.41)
       .inner
        width: 16px
        height: 16px
        border-radius: 50%
        background: rgb(0,160,220)
        transition: all .4s linear
</style>




