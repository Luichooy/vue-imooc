<template>
  <div class="ratings">
    <div class="ratings-top clearfix">
      <div class="left border-width">
        <p class="mark">{{seller.score}}</p>
        <p class="mark-type">综合评分</p>
        <p class="mark-desc">高于周边商家{{seller.rankRate}}%</p>
      </div>
      <div class="right">
        <div class="score-wrapper">
          <span class="label">服务态度</span>
          <div class="star-wrapper">
            <star :size="24" :score="seller.serviceScore"></star>
          </div>
          <span class="score">{{seller.serviceScore}}</span>
        </div>
        <div class="score-wrapper">
          <span class="label">商品评分</span>
          <div class="star-wrapper">
            <star :size="24" :score="seller.foodScore"></star>
          </div>
          <span class="score">{{seller.foodScore}}</span>
        </div>
        <div class="score-wrapper">
          <span class="label">送达时间</span>
          <span class="delivery-time">{{seller.deliveryTime}}</span>
        </div>
      </div>
    </div>
    <split></split>
    <div class="ratings-wrapper">
      <ratingselect :select-type="selectType" :only-content="onlyContent" :desc="desc"
                    :ratings="ratings" v-on:toggleType="toggleType"
                    v-on:toggleOnlyContent="toggleOnlyContent"></ratingselect>
      <div class="ratings-list">
        <ul>
          <li class="rating-item" v-for="rating in ratings">
            <div class="avatar-wrapper">
              <img :src="rating.avatar" alt="">
            </div>
            <div class="rating-detail">
              <div>
                <span class="username">{{rating.username}}</span>
                <span class="rating-time">{{rating.rateTime}}</span>
              </div>
              <div>
                <div class="star-wrapper"></div>
                <span class="dilivery-time">{{rating.deliveryTime}}分钟送达</span>
              </div>
              <div>{{rating.text}}</div>
              <div>
                <div class="recommend-wrapper">
                  <ul>
                    <li v-for="recommend in rating.recommend">{{recommend}}</li>
                  </ul>
                </div>
              </div>
            </div>
          </li>
        </ul>
      </div>
    </div>
  </div>

</template>

<script type="text/ecmascript-6">
  import star from '../star/star.vue';
  import split from '../split/split.vue';
  import ratingselect from '../ratingselect/ratingselect.vue';

  const ERR_OK = 0;
  //  const POSITIVE = 0;
  //  const NEGATIVE = 1;
  const ALL = 2;

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
        onlyContent: false,
        desc: {
          all: '全部',
          positive: '满意',
          negative: '不满意'
        }
      };
    },
    methods: {
      toggleType(type) {
        console.log(type);
        this.selectType = type;
      },
      toggleOnlyContent(onlyContent) {
        this.onlyContent = onlyContent;
      }
    },
    created() {
      this.$http.get('/api/ratings').then(res => {
        res = res.body;
        if (res.errno === ERR_OK){
          this.ratings = res.data;
        } else {
          console.log('请求失败');
        }
      }, error => {
        console.log(error);
      });
    },
    components: {
      star,
      split,
      ratingselect
    }
  };
</script>

<style lang="stylus" rel="stylesheet/stylus">
  @import "../../common/stylus/mixin.styl"
  .ratings
    .ratings-top
      padding:18px 0
      .left
        width: 138px
        float:left
        border-right(rgba(7,17,27,0.1))
        box-sizing :border-box
        text-align:center
        .mark
          line-height: 28px
          font-size: 24px
          color:rgb(255,153,0)
        .mark-type
          margin: 6px 0 8px 0
          line-height: 12px
          font-size: 12px
          color:rgb(7,17,27)
        .mark-desc
          margin-bottom: 6px
          line-height: 10px
          font-size: 10px
          color: rgba(7,17,27,0.5)
      .right
        float: left
        padding: 0 24px
        & > .score-wrapper + .score-wrapper
          margin-top: 8px
        .score-wrapper
          .label
            margin-right: 12px
            line-height 18px
            font-size: 12px
            color: rgb(7,17,27)
          .star-wrapper
            display :inline-block
            margin-right: 12px
          .score
            line-height: 18px
            font-size: 12px
            color: rgb(255,153,0)
          .delivery-time
            line-height: 18px
            font-size: 12px
            color: rgb(147,153,159)

    .ratings-wrapper
      .ratings-list
        padding: 18px
        & > .rating-item + .rating-item
          border-top(red)
</style>
