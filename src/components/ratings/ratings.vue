<template>
    <div class="ratings" ref="ratings">
        <div class="ratings-content">
            <div class="overview">
                <div class="overview-left">
                    <h1 class="score">{{seller.score}}</h1>
                    <div class="title">综合评分</div>
                    <div class="rank">高于周边商家{{seller.rankRate}}%</div>
                </div>
                <div class="overview-right">
                    <div class="score-wrapper">
                        <span class="title">服务态度</span>
                        <star :size="36" :score="seller.serviceScore"></star>
                        <span class="score">{{seller.serviceScore}}</span>
                    </div>
                    <div class="score-wrapper">
                        <span class="title">商品评分</span>
                        <star :size="36" :score="seller.foodScore"></star>
                        <span class="score">{{seller.foodScore}}</span>
                    </div>
                    <div class="delivery-wrapper">
                        <span class="title">送达时间</span>
                        <span class="delivery-time">{{seller.deliveryTime}}分钟</span>
                    </div>
                </div>
            </div>
            <split></split>
            <ratingselects @select="selectRating" @toggle="toggleContent" :selectType="selectType" :onlyContent="onlyContent" :ratings="ratings"></ratingselects>
            <div class="rating-wrapper">
              <ul>
                  <li v-show="needShow(rating.rateType,rating.text)" v-for="(rating,index) in ratings" :key="index" class="rating-item">
                      <div class="avatar">
                          <img :src="rating.avatar" alt="" width="28" height="28">
                      </div>
                      <div class="content">
                          <h1 class="name">{{rating.username}}</h1>
                          <div class="star-wrapper">
                              <star :size="24" :score="rating.score"></star>
                              <span class="delivery" v-show="rating.deliveryTime">{{rating.deliveryTime}}分钟</span>
                          </div>
                          <p class="text">{{rating.text}}</p>
                          <div class="recommend" v-show="rating.recommend && rating.recommend.length">
                              <span class="icon-thumb_up"></span>
                              <span class="item" v-for="(item,index) in rating.recommend" :key="index">{{item}}</span>
                          </div>
                          <div class="time">{{rating.rateTime | formaDate}}</div>
                      </div>
                  </li>
              </ul>
            </div>
        </div>
    </div>
</template>
<script type="text/ecmascript-6">
  import BScroll from 'better-scroll';
  import star from '../star/star.vue';
  import split from '../split/split.vue';
  import ratingselects from '../ratingSelect/ratingSelect.vue';
  const ALL = 2;
  const ERR_OK = 0;
  export default {
      props: {
          seller: {
              type: Object
          }
      },
      data() {
          return {
              ratings: [],
              selectType: ALL,
              onlyContent: true
          }
      },
      created() {
          this.$http.get('/api/ratings').then((response) => {
             response = response.body
             if(response.errno == ERR_OK){
                 this.ratings = response.data;
                 this.$nextTick(() => {
                     this.scroll = new BScroll(this.$refs.ratings,{
                         click: true
                     })
                 })
             }
          })
      },
      methods: {
          selectRating(type) {
              this.selectType = type;
          },
          toggleContent() {
              this.onlyContent = !this.onlyContent;
          },
          needShow(type,text) {
              if(this.onlyContent && !text){
                  return false;
              }
              if(this.selectType == ALL){
                  return true;
              }else{
                  return type === this.selectType
              }
          }
      },
      components: {
          star,
          split,
          ratingselects
      },
      filters: {
        formaDate(time) {
           var date = new Date(time);
           return date.getFullYear() + '-'+ (date.getMonth()+ 1) + '-'+ date.getDay()+' '+ date.getHours() + ':' + date.getMinutes(); 
        }
      }
  }
</script>
<style lang="stylus" rel="stylesheet/stylus"> 
  @import "../../common/stylus/mixin.styl";
    .ratings
     position: absolute
     top: 174px
     bottom: 0
     left: 0
     width: 100%
     overflow: hidden
     .overview
      display: flex
      padding: 18px 0
      .overview-left
       flex: 0 0 0 137px
       width: 137px
       border-right: 1px solid rgba(7, 17, 27, 0.1)
       text-align: center
       padding: 6px 0
       .score
        font-size: 24px
        color: rgb(255,153,0)
        line-height: 28px
        margin-bottom: 6px
       .title
        font-size: 12px
        color: rgb(7,17,27)
        line-height: 12px
        margin-bottom: 8px
       .rank
         font-size: 10px
         color: rgb(1,17,27)
         line-height: 10px
      .overview-right
       flex: 1
       padding: 6px 0 6px 24px
       .score-wrapper
        font-size: 0
        margin-bottom: 8px
        .title
         font-size: 12px
         color: rgb(7,17,27)
         line-height: 18px
         display: inline-block
         vertical-align: top
        .star
         display: inline-block
         padding: 0 12px
         font-size: 18px
        .score
         font-size: 12px
         color: rgb(255,153,0)
         line-height: 18px
         vertical-align: top
       .delivery-wrapper
        font-size: 0
        .title
         font-size: 12px
         line-height: 18px
         color: rgb(7,17,27)
         margin-right: 12px
        .delivery-time
         font-size: 12px
         line-height: 18px
         color: rgb(147,153,159)
     .rating-wrapper
      padding: 0 18px
      .rating-item
       display: flex;
       border-1px(rgba(7, 17, 27, 0.1));
       padding: 18px 0
       .avatar
        width: 28px
        height: 28px
        margin-right: 12px
        img
         border-radius: 50%
       .content
        flex: 1
        position: relative
        .name
         font-size: 10px
         color: rgb(7,17,27)
         line-height: 12px
         margin-bottom: 4px
        .star-wrapper
         margin-bottom: 6px
         .star
          display: inline-block
          margin-right: 6px
         .delivery
          display: inline-block
          font-size: 10px
          font-weight: 200
          line-height: 12px
          color: rgb(147,153,159)
        .text
         font-size: 12px
         color: rgb(7,17,27)
         line-height: 18px
         margin-bottom: 8px
        .recommend
         line-height: 16px
         font-size: 0
         .icon-thumb_up, .item
          display: inline-block
         .icon-thumb_up
          font-size: 12px
          color: rgb(0,160,220)
          margin-right: 8px
         .item
           margin: 0 8px 4px 0
           font-size: 9px
           border: 1px solid rgba(7,17,27,0.1)
           border-radius: 2px
           padding: 0 6px
           color: #93999f
        .time
         position: absolute
         top: 0
         right: 0
         font-size: 10px
         font-weight: 200
         color: rgb(147,153,159)
         line-height: 12px


         
         
       

         

        

        
        


</style>
