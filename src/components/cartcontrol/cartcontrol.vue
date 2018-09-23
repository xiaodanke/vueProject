<template>
    <div class="cartcontrol">
        <div class="cart-dercase" v-show="food.count > 0" @click="disCount">
            <span class="inner icon-remove_circle_outline"></span>
        </div>
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
            },
            disCount(event) {
                if(!event._constructed){
                    return ;
                }
                this.food.count--;
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
      .inner
       line-height: 24px
       font-size: 24px
       color: #00a0dc
       display: inline-block
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
