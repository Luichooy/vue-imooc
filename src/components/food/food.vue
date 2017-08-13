<template>
  <div class="food" v-show="showFlag" ref="food">
    <div class="food-content">
      <div class="image-header">
        <img :src="food.image">
        <div class="back" @click="hide()">
          <i class="icon-arrow_lift"></i>
        </div>
      </div>
      <div class="content">
        <h1 class="title">{{food.name}}</h1>
        <div class="detail">
          <span class="sell-count">月售{{food.sellCount}}份</span>
          <span class="rating">好评率{{food.rating}}</span>
        </div>
        <div class="price">
          <span class="nowPrice">￥{{food.price}}</span>
          <span class="oldPrice" v-if="food.oldPrice">￥{{food.oldPrice}}</span>
        </div>
        <div class="cartcontrol-wrapper">
          <cartcontrol :food="food"></cartcontrol>
        </div>
        <div @click.stop="addFirst($event)" class="buy" v-show="!food.count || food.count === 0">加入购物车</div>
      </div>
      <split v-show="food.info"></split>
      <div class="info" v-show="food.info">
        <div class="title">商品介绍</div>
        <div class="text">{{food.info}}</div>
      </div>
      <split></split>
      <div class="rating">
        <div class="title">商品评价</div>
        <ratingselect :select-type="selectType" :only-content="onlyContent" :desc="desc"
                      :ratings="food.ratings"></ratingselect>
      </div>
    </div>
  </div>
</template>
<script type="text/ecmascript-6">

  import BScroll from 'better-scroll';
  import cartcontrol from '../cartcontrol/cartcontrol.vue';
  import split from '../split/split.vue';
  import ratingselect from '../ratingselect/ratingselect.vue';

  //  const POSITIVE = 0;
  //  const NEGATIVE = 1;
  const ALL = 2;

  export default {
    props: {
      food: {
        type: Object
      }
    },
    data(){
      return {
        showFlag: false,
        selectType: ALL,
        onlyContent: true,
        desc: {
          all: '全部',
          positive: '推荐',
          negative: '吐槽'
        }
      };
    },
    methods: {
      show(){
        this.selectType = ALL;
        this.onlyContent = true;

        this.$nextTick(() => {
          if (!this.scroll) {
            this.scroll = new BScroll(this.$refs.food, {
              click: true
            });
          } else {
            this.scroll.refresh();
          }
        });
        this.showFlag = true;
      },
      hide(){
        this.showFlag = false;
      },
      addFirst(event){
        if (!event._constructed) {
          return;
        }
        this.$set(this.food, 'count', 1);
      }
    },
    components: {
      cartcontrol,
      split,
      ratingselect
    }
  };
</script>
<style lang="stylus" rel="stylesheet/stylus">
  .food
    position: fixed
    left: 0
    top: 0
    bottom: 48px
    z-index: 9
    width: 100%
    background-color: #fff;
    .food-content
      .image-header
        position: relative
        width: 100%
        height: 0
        padding-top: 100%
        img
          position: absolute
          top: 0
          left: 0
          width: 100%
          height: 100%
        .back
          position: absolute
          left: 0
          top: 10px
          .icon-arrow_lift
            display: block
            padding: 10px
            font-size: 20px
            color: #fff
      .content
        position: relative
        padding: 18px
        .title
          line-height: 14px
          font-size: 14px
          font-weight: bold
          color: rgb(7, 17, 27)
        .detail
          margin: 8px 0 18px 0
          .sell-count, .rating
            line-height: 10px
            font-size: 10px
            color: rgb(147, 153, 159)
          .sell-count
            margin-right: 12px
        .price
          .nowPrice
            margin-right: 12px
            line-height: 24px
            font-size: 14px
            font-weight: 700
            color: rgb(240, 20, 20)
          .oldPrice
            line-height: 24px
            font-size: 10px
            font-weight: 700
            color: rgb(147, 153, 159)
            text-decoration line-through
        .cartcontrol-wrapper
          position: absolute
          right: 12px
          bottom: 12px
        .buy
          position: absolute
          right: 18px
          bottom: 18px
          height: 24px
          line-height: 24px
          padding: 0 12px
          border-radius: 12px
          font-size: 10px
          color: #fff
          background-color: rgb(0, 160, 220)
          z-index: 10
      .info
        padding: 18px
        .title
          line-height: 14px
          margin-bottom: 6px
          font-weight: 14px
          color: rgb(7, 17, 27)
        .text
          padding: 0 8px
          line-height: 24px
          font-size: 12px
          font-weight: 200
          color: rgb(77, 85, 93)
      .rating
        padding-top: 18px;
        .title
          line-height: 14px
          margin-left: 18px
          font-weight: 14px
          color: rgb(7, 17, 27)
</style>
