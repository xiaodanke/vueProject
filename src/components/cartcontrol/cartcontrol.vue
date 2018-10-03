<template>
    <div class="cartcontrol">
        <transition name="move">
        <div class="cart-dercase" v-show="food.count > 0" @click="disCount">
            <span class="inner icon-remove_circle_outline"></span>
        </div>
        </transition>
        <div class="cart-count" v-show="food.count > 0">{{food.count}}</div>
        <div class="cart-discount" @click="addCount">
            <span class="cart-add icon-add_circle"></span>
        </div>
    </div>
</template>
<script type="text/ecmascript-6">
    import Vue from 'vue';
    export default {
        props: {
            food: {
                type: Object
            }
        },
        methods: {
            addCount(event) {
              if(!event._constructed){
                  return;
              }
              if(!this.food.count){
                  Vue.set(this.food ,'count',1)
              }else{
                  this.food.count++;
              }
              this.$emit('add',event.target);
            },
            disCount(event) {
                if(!event._constructed){
                    return ;
                }
                if(this.food.count){
                    this.food.count--;
                }
            }
        }
    }
</script>
<style lang="stylus" rel="stylesheet/stylus">
    .cartcontrol
     font-size: 0
     .cart-dercase
      padding: 6px
      display: inline-block
      transform: translate3d(0,0,0)
      opacity: 1
      .inner
       line-height: 24px
       font-size: 24px
       color: #00a0dc
       display: inline-block
       transition: all .4s linear
       transform: rotate(0)
      &.move-enter-active,&.move-leave-active
        transition: all .4s linear
      &.move-enter,&.move-leave-active
       opacity: 0
       transform: translate3d(24px,0,0)
       .inner
         transform: rotate(180deg)
     .cart-discount
      padding: 6px
      display: inline-block
      .cart-add
        line-height: 24px
        font-size: 24px
        color: #00a0dc
     .cart-count
      display: inline-block
      font-size: 10px
      vertical-align: top
      padding-top: 6px
      line-height: 24px
      width: 12px
      text-align: center

</style>
