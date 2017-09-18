<template>
  <div class="ratings">
    <div class="ratings-wrapper" ref="ratings-wrapper">
      <div class="ratings-content">
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
              <span class="delivery-time">{{seller.deliveryTime}}分钟</span>
            </div>
          </div>
        </div>
        <split></split>
        <div class="ratings-list-wrapper">
          <ratingselect :select-type="selectType" :only-content="onlyContent" :desc="desc"
                        :ratings="ratings" v-on:toggleType="toggleType"
                        v-on:toggleOnlyContent="toggleOnlyContent"></ratingselect>
          <div class="ratings-list">
            <ul>
              <li class="rating-item border-height" v-for="rating in ratings" v-show="needShow(rating)">
                <div class="avatar-wrapper">
                  <img :src="rating.avatar" alt="" width="100%" height="100%">
                </div>
                <div class="rating-detail">
                  <div class="rating-user clearfix">
                    <span class="username">{{rating.username}}</span>
                    <span class="rating-time">{{rating.rateTime | formatDate}}</span>
                  </div>
                  <div class="rating-star">
                    <div class="star-wrapper">
                      <star :size="36" :score="rating.score"></star>
                    </div>
                    <span class="dilivery-time">{{rating.deliveryTime}}分钟送达</span>
                  </div>
                  <div class="rating-text">{{rating.text}}</div>
                  <div class="recommend-wrapper">
                    <i class="rating-icon"
                       :class="{'icon-thumb_up': rating.rateType === 0, 'icon-thumb_down': rating.rateType === 1}"></i>
                    <ul class="recommend-list">
                      <li class="recommend-item" v-for="recommend in rating.recommend">{{recommend}}</li>
                    </ul>
                  </div>
                </div>
              </li>
            </ul>
          </div>
        </div>
      </div>
    </div>
    <shopcart :select-foods="selectFoods" :delivery-price="seller.deliveryPrice"
              :min-price="seller.minPrice"></shopcart>
  </div>

</template>

<script type="text/ecmascript-6">
  import BScroll from 'better-scroll';
  import star from '../star/star.vue';
  import split from '../split/split.vue';
  import ratingselect from '../ratingselect/ratingselect.vue';
  import shopcart from '../shopcart/shopcart.vue';
  import {formatDate} from '../../common/js/date';

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
    created() {
      this.$http.get('/api/ratings').then(res => {
        res = res.body;
        if (res.errno === ERR_OK) {
          this.ratings = res.data;

          this.$nextTick(function () {
            if (!this.scroll) {
              this.scroll = new BScroll(this.$refs['ratings-wrapper'], {
                click: true
              });
            } else {
              this.scroll.refresh();
            }
          });
        } else {
          console.log('请求失败');
        }
      }, error => {
        console.log(error);
      });
    },
    methods: {
      needShow(rating) {
        let type = this.selectType;
        let text = rating.text;
        if (this.onlyContent && !text) {
          return false;
        }
        if (this.selectType === ALL) {
          return true;
        } else {
          return rating.rateType === type;
        }
      },
      toggleType(type) {
        console.log(type);
        this.selectType = type;
        this.$nextTick(() => {
          this.scroll.refresh();
        });
      },
      toggleOnlyContent(onlyContent) {
        this.onlyContent = onlyContent;
        this.$nextTick(() => {
          this.scroll.refresh();
        });
      }
    },
    filters: {
      formatDate(time) {
        let date = new Date(time);
        return formatDate(date, 'yyyy-MM-dd hh:mm:ss');
      }
    },
    components: {
      star,
      split,
      ratingselect,
      shopcart
    }
  };
</script>

<style lang="stylus" rel="stylesheet/stylus">
  @import "../../common/stylus/mixin.styl"
  .ratings
    .ratings-wrapper
      position : fixed
      top: 174px
      bottom: 48px
      left: 0
      right: 0
      overflow: hidden
      .ratings-top
        padding: 18px 0
        .left
          width: 138px
          float: left
          border-right(rgba(7, 17, 27, 0.1))
          box-sizing: border-box
          text-align: center
          .mark
            line-height: 28px
            font-size: 24px
            color: rgb(255, 153, 0)
          .mark-type
            margin: 6px 0 8px 0
            line-height: 12px
            font-size: 12px
            color: rgb(7, 17, 27)
          .mark-desc
            margin-bottom: 6px
            line-height: 10px
            font-size: 10px
            color: rgba(7, 17, 27, 0.5)
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
              color: rgb(7, 17, 27)
            .star-wrapper
              display: inline-block
              margin-right: 12px
            .score
              line-height: 18px
              font-size: 12px
              color: rgb(255, 153, 0)
            .delivery-time
              line-height: 18px
              font-size: 12px
              color: rgb(147, 153, 159)

      .ratings-list-wrapper
        .ratings-list
          padding: 0 18px
          .rating-item + .rating-item
            border-top(rgba(7, 17, 27, 0.1))
          .rating-item
            display: flex
            padding: 18px 0
          .avatar-wrapper
            flex: 0 0 28px
            padding-right: 12px
            width: 28px
            height: 28px
            img
              border-radius: 50%
          .rating-detail
            flex: 1 1 auto
            /*vertical-align: top*/
            .rating-user
              width: 100%
              margin-bottom: 4px
              .username
                float: left
                line-height: 12px
                font-size: 10px
                color: rgb(7, 17, 27)
              .rating-time
                float: right
                line-height: 12px
                font-size: 10px
                font-weight: 200
                color: rgb(147, 153, 159)
            .rating-star
              font-size: 0
              .star-wrapper
                display: inline-block
                vertical-align: middle
                margin-right: 12px
              .dilivery-time
                display: inline-block
                vertical-align: middle
                line-height: 12px
                font-size: 10px
                font-weight: 200
                color: rgb(147, 153, 159)
            .rating-text
              margin: 6px 0 8px 0
              line-height: 18px
              font-size: 12px
              color: rgb(7, 17, 27)
            .recommend-wrapper
              .rating-icon
                display: inline-block
                margin-right: 8px
                line-height: 16px
                font-size: 12px
                &.icon-thumb_up
                  color: rgb(0, 160, 220)
                &.icon-thumb_down
                  color: rgb(183, 187, 191)
              .recommend-list
                display: inline-block
                font-size: 0
                .recommend-item
                  display: inline-block
                  margin-right: 8px
                  padding: 0 6px
                  line-height: 16px
                  border: 1px solid rgba(7, 17, 27, 0.1)
                  font-size: 9px
                  color: rgb(147, 153, 159)
</style>
