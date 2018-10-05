<template>
    <transition name="move"> 
        <div class="food" v-show="showFlag" ref="foodll">
             <div class="food-content">
                <div class="img-header">
                    <img :src="food.image">
                    <div class="back" @click="hide">
                        <i class="icon-arrow_lift"></i>
                    </div>
                </div>
                <div class="content">
                    <div class="title">{{food.name}}</div>
                    <div  class="detail">
                        <span class="sell-count">月售{{food.sellCount}}份</span>
                        <span class="rating">好评率{{food.rating}}%</span>
                    </div>
                    <div class="price">
                        <span class="now">￥{{food.price}}</span><span class="old" v-show="food.oldPrice">￥{{food.oldPrice}}</span>
                    </div>
                    <div class="cartcontrol-wrapper">
                        <cartcontrol @add="addFood" :food="food"></cartcontrol>
                    </div>
                    <transition name="fade">
                        <div class="buy" v-show="!food.count || food.count === 0" @click="addFirst">
                            加入购物车
                        </div>
                    </transition>
                </div>
                <split v-show="food.info"></split>
                <div class="info" v-show="food.info">
                    <h1 class="title">商品信息</h1>
                    <p class="text">{{food.info}}</p>
                </div>
                <split></split>
                <div class="rating">
                    <h1 class="title">商品评价</h1>
                    <ratingSelect @toggle="toggleContent" @select="selectRating" :selectType="selectType" :onlyContent="onlyContent" :desc="desc" :ratings="food.ratings"></ratingSelect>
                </div>
                <div class="rating-wrapper">
                    <ul v-show="food.ratings && food.ratings.length">
                        <li v-show="needShow(rating.rateType,rating.text)" v-for="(rating,index) in food.ratings" :key="index" class="rating-item border-1px">
                            <div class="user">
                                <span class="name">{{rating.username}}</span>
                                <img :src="rating.avatar" alt="" width="12" height="12" class="avatar">
                            </div>
                            <div class="time">{{rating.rateTime | formaDate}}</div>
                            <p class="text">
                                <span :class="{'icon-thumb_up':rating.rateType === 0, 'icon-thumb_down':rating.rateType === 1}"></span>{{rating.text}}
                            </p>                
                        </li>
                    </ul>
                    <div class="no-rating" v-show="!food.ratings || !food.ratings.length">暂无评价</div>
                </div>
            </div>  
        </div>
    </transition>
</template>
<script type="text/ecmascript-6">
import cartcontrol from "../cartcontrol/cartcontrol.vue";
import ratingSelect from "../ratingSelect/ratingSelect.vue";
import split from "../split/split.vue";
import BScroll from 'better-scroll';
import Vue from 'vue';

/* */
const POSITIVE = 0;
const NEGAtiVe = 1;
const ALL = 2;

export default {
    props: {
        food: {
            type: Object
        }
    },
    data() {
        return {
            showFlag: false,
            selectType: ALL,
            onlyContent: true,
            desc: {
                all: '全部',
                positive: '推荐',
                negative: '吐槽'
            }
        }
    },
    methods: {
        show() {
            this.showFlag = true;
            //初始化一些属性
            this.selectType = ALL;
            this.onlyContent = true;
            this.$nextTick(() => {
                if(!this.scroll){
                     this.scroll = new BScroll(this.$refs.foodll, {
                      click: true
                     });
                }else{
                    this.scroll.refresh();
                }
            })
        },
        hide() {
            this.showFlag = false;
        },
        addFood(target) {
            this.$emit('add',target)
        },
        addFirst(event) {
            if(!event._constructed){
                return;
            }
            this.$emit('add',event.target);
            Vue.set(this.food,'count',1);
        },
        needShow(type,text){
            if(!this.onlyContent && !text){
                return false;
            }
            if(this.selectType == ALL){
                return true;
            }else{
                return  type === this.selectType;
            }
        },
        toggleContent() {
            this.onlyContent = !this.onlyContent;
            this.$nextTick(() => {
                this.scroll.refresh();
            })
        },
        selectRating(type) {
            this.selectType = type;
            this.$nextTick(() => {
                this.scroll.refresh();
            })
        }
    },
    filters: {
        formaDate(time) {
           var date = new Date(time);
           return date.getFullYear() + '-'+ (date.getMonth()+ 1) + '-'+ date.getDay()+' '+ date.getHours() + ':' + date.getMinutes(); 
        }
    },
    components: {
        cartcontrol,
        split,
        ratingSelect
    }
}
</script>

<style lang="stylus" rel="stylesheet/stylus">
 @import "../../common/stylus/mixin.styl";
    .food
     position: fixed
     left: 0
     top: 0
     bottom: 48px
     z-index: 30
     width: 100%
     background: #ffffff
     transform: translate3d(0,0,0)
     &.move-enter-active,&.move-leave-active
      transition: all .3s linear
     &.move-enter,&.move-leave-active
      transform: translate3d(100%,0,0)
     .img-header
      width: 100%
      height: 0
      padding-top: 100%
      position:relative
      img 
       position: absolute
       left: 0
       top: 0
       width: 100%
       height: 100%
      .back
       position: absolute
       left: 0
       top: 10px
       .icon-arrow_lift
        padding: 10px
        font-size: 20px
        display: block
        color: #ffffff
     .content
      position: relative
      padding: 18px
      .title
       line-height: 14px
       margin-bottom: 8px
       font-size: 14px
       font-weight: 700
       color: rgb(7, 17, 27)
      .detail
       margin-bottom: 18px
       line-height: 10px
       height: 10px
       font-size: 0
       .sell-count,.rating
         font-size: 10px
         color: rgb(147, 153, 159)
       .sell-count
        margin-right: 12px
     .price
       font-weight: 700
       line-height: 24px
       .now
        margin-right: 8px
        font-size: 14px
        color: rgb(240, 20, 20)
       .old
        text-decoration: line-through
        font-size: 10px
        color: rgb(147,153,159)
     .cartcontrol-wrapper
      position: absolute
      right: 12px
      bottom: 12px
     .buy
      position: absolute
      right: 18px
      bottom: 18px
      z-index: 10
      height: 24px
      line-height: 24px
      padding: 0 12px
      box-sizing: border-box
      border-radius: 12px
      font-size: 10px
      color: #ffffff
      background: rgb(0, 160, 220)
      opacity: 1
      &.fade-enter-active,&.fade-leave-active
       transition: all .2s
      &.fade-enter,&.fade-leave-active
       opacity: 0
       z-index: -1
     .info
      padding: 18px
      .title
       line-height: 14px
       margin-bottom: 6px
       font-size: 14px
       color: rgb(7,17,27)
      .text
       line-height: 24px
       padding: 0 8px
       font-szie: 12px
       color: rgb(77,85,93)
     .rating
      padding-top: 8px
      .title
       line-height: 14px
       font-size: 14px
       margin-left: 18px
       color: rgb(7, 17, 27)
     .rating-wrapper
      padding: 0 18px
      .rating-item
       position: relative
       padding: 16px 0
       border-1px(rgba(7, 17, 27, 0.1))
       .user
        position: absolute
        right: 0
        top: 16px
        line-height: 12px
        font-size: 0
        .name
         display: inline-block
         vertical-align: top
         font-size: 10px
         margin-right: 6px
         color: rgb(147, 153, 159)
         .avatar
          border-radius: 50%
       .time
        margin-bottom: 6px
        line-height: 12px
        font-size: 10px
        color: rgb(147, 153, 159)
       .text
         line-height: 16px
         font-size: 12px
         color: rgb(7, 17, 27)
         .icon-thumb_up, .icon-thumb_down
            margin-right: 4px
            line-height: 16px
            font-size: 12px
         .icon-thumb_up
            color: rgb(0, 160, 220)
         .icon-thumb_down
            color: rgb(147, 153, 159)
     .no-rating
      padding: 16px 0
      font-size: 12px
      color: rgb(147, 153, 159)
        
       
       

       
    
</style>


