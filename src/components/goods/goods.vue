<template>
    <div>
        <div class="goods">
            <div class="menu-wrapper" ref="menuWrapper">
                <li v-for="(item,index) in goods" class="menu-item" :key="index" :class="{'current':currentIndex === index}" ref="menuList">
                    <span class="text border-1px" @click="selectMenu(index,$event)">
                        <span v-show="item.type > 0" class="icon" :class="classMap[item.type]"></span>{{item.name}}
                    </span>
                </li>
            </div>
            <div class="foods-wrapper" ref="foodsWrapper">
                <ul>
                    <li v-for="(item,index) in goods" :key="index" class="food-list" ref="foodList">
                        <h1 class="title">{{item.name}}</h1>
                        <ul>
                            <li @click="selectFood(food,$event)" v-for="(food,index) in item.foods" :key="index" class="food-item">
                                <div class="icon">
                                    <img :src="food.icon" alt="" width="57" height="57">
                                </div>
                                <div class="content">
                                    <div class="name">{{food.name}}</div>
                                    <div class="des">{{food.description}}</div>
                                    <div class="extra">
                                      <span class="count">月售{{food.sellCount}}份</span><span>好评率{{food.rating}}%</span>
                                    </div>
                                    <div class="price">
                                        <span class="now">￥{{food.price}}</span>
                                        <span class="old" v-show="food.oldPrice">￥{{food.oldPrice}}</span>
                                    </div>
                                    <div class="cartcontrol-wrapper">
                                        <cartcontrol :food="food" @add="addFoods"></cartcontrol>
                                    </div>
                                </div>
                            </li>
                        </ul>
                    </li>
                </ul>
            </div>
        </div>
        <shopcart ref="shopcart" :selectFoods="selectFoods" :deliveryPrice="seller.deliveryPrice"
                :minPrice="seller.minPrice"></shopcart>
        <food @add="addFoods" :food="selectedFood" ref="food"></food>
    </div>
</template> 
<script type="text/ecmascript-6">
    import BScroll from 'better-scroll';
    import shopcart from '../shopcart/shopcart.vue';
    import cartcontrol from '../cartcontrol/cartcontrol.vue';
    import food from '../food/food.vue';
    const ERROR = 0;
    export default {
        props:{
            seller: {
                type: Object
            }
        },
        data() {
            return {
                goods: [],
                listHeight: [],
                scrollY: 0,
                selectedFood: {}
            }
        },
        created() {
            this.classMap = ['decrease', 'discount', 'special', 'invoice', 'guarantee'];
            this.$http.get('api/goods').then( (res) => {
                var res = res.body;
                if(res.errno == ERROR){
                    this.goods = res.data;
                    this.$nextTick(() => {
                        this._initScroll();
                        this._calculateHeight();
                    })
                }
            })
        },
        computed: {
            currentIndex() {
                for(let i = 0; this.listHeight.length;i++){
                    let height1 = this.listHeight[i];
                    let height2 = this.listHeight[i+1];
                    if(!height2 || (this.scrollY >= height1 && this.scrollY < height2)){
                        this.menuList = this.$refs.menuList;
                        let el = this.menuList[i];
                        this.menuscroll.scrollToElement(el,300);
                        return i;
                    }
                }
                return 0;
            },
             selectFoods() {
                let foods = [];
                this.goods.forEach((good) => {
                   good.foods.forEach((food) => {
                       if(food.count){
                           foods.push(food);
                       }
                   })
                })
                return foods;
            }
        },
        methods: {
            selectMenu(index,event) {
                if(!event._constructed){
                    return;
                }
                let foodList = this.$refs.foodList;
                let el = foodList[index];
                this.foodsScroll.scrollToElement(el,300);
            },
            addFoods(target) {
                this._drop(target);
            },
            _drop(target) {
                this.$nextTick(() => {
                    this.$refs.shopcart.drop(target);
                })
            },
            selectFood(food,event) {
                if(!event._constructed){
                    return ;
                }
                this.selectedFood = food;
                this.$refs.food.show();
            },
            _initScroll() {
                this.menuscroll = new BScroll(this.$refs.menuWrapper,{
                    click: true
                })
                this.foodsScroll = new BScroll(this.$refs.foodsWrapper,{
                    click: true,
                    probeType: 3
                })
                this.foodsScroll.on('scroll',(pos) =>{
                    if(pos.y <= 0){
                        this.scrollY = Math.abs(Math.round(pos.y));
                    }
                })
            },
            _calculateHeight() {
                let foodList = this.$refs.foodList;
                let height = 0;
                this.listHeight.push(height);
                for(var i = 0;i < foodList.length;i++){
                    let item = foodList[i];
                    height += item.clientHeight;
                    this.listHeight.push(height);
                }
            }
        },
        components: {
          shopcart,
          cartcontrol,
          food
        }
    }
</script>
<style lang="stylus" rel="stylesheet/stylus">
    @import "../../common/stylus/mixin.styl"
    .goods
     display: flex
     position: absolute
     top: 174px
     width: 100%
     bottom: 46px
     overflow: hidden
     .menu-wrapper
      flex: 0 0 80px
      width: 80px
      background: #f3f5f7
      .menu-item
       display: table
       width: 54px
       height: 56px
       padding: 0 12px
       line-height: 14px
       &.current
        position: relative
        z-index: 10
        margin-top: -1px
        background: #ffffff
        font-weight: 700
       .icon
        display: inline-block
        vertical-align: top
        width: 12px
        height: 12px
        background-size: 12px 12px
        background-repeat: no-repeat
        margin-right: 2px
        &.decrease
         bg-image('decrease_3')
        &.discount
         bg-image('discount_3')
        &.guarantee
         bg-image('guarantee_3')
        &.invoice
         bg-image('invoice_3')
        &.special
         bg-image('special_3')
       .text
        display: table-cell
        height: 56px
        vertical-align: middle
        border-1px(rgba(7, 17, 27, 0.1))
        font-size: 12px
     .foods-wrapper
      flex: 1
      .title
       height: 26px
       padding-left: 14px
       border-left: 2px solid #d9dde1
       background: #f3f5f7
       font-size: 12px
       line-height: 26px
       color: rgb(147,153,159)
     .food-item
        display: flex
        margin: 18px
        padding-bottom: 18px
        border-1px(rgba(7, 17, 27, 0.1))
        &:last-child
         border-none()
         margin-bottom: 0
      .icon 
        flex: 0 0 57px
        margin-right: 10px
      .content
        flex: 1
        .name
         font-size: 14px
         color: rgb(7,17,27)
         line-height: 14px
         margin: 2px 0 8px 0
        .des, .extra
         font-size: 10px
         color:rgb(147,153,159)
         line-height: 10px
        .des
         margin-bottom: 8px
         line-height: 12px
        .extra
         white-space nowrap
         .count
          margin-right: 12px
        .price
         font-weight: 700
         line-height: 24px
         .now
          font-size: 14px
          color: rgb(240,20,20)
          padding-right: 8px
         .old
          text-decoration: line-through
          font-size: 10px
          color: rgb(147, 153, 159)
        .cartcontrol-wrapper
         position: absolute
         right: 0
         bottom: 12px
</style>
