<template>
  <div class="seller" ref="seller">
    <div class="seller-content">
      <div class="seller-top">
        <div class="seller-info border-height">
          <h1 class="seller-name">{{seller.name}}</h1>
          <div class="seller-desc">
            <div class="star-wrapper">
              <star :size="36" :score="seller.score"></star>
            </div>
            <span class="rating-count">（{{seller.ratingCount}}）</span>
            <span class="seller-count">月售690单</span>
          </div>
          <div class="favorite-wrapper" @click="toggleFavorite">
            <span class="icon-favorite" :class="{'active': favorite}"></span>
            <span class="favorite-text">{{favoriteText}}</span>
          </div>
        </div>
        <ul class="delivery-list">
          <li class="delivery-item">
            <p class="label">起送价</p>
            <p class="value-wrapper">
              <span class="value">{{seller.minPrice}}</span>
              <span class="unit">元</span>
            </p>
          </li>
          <li class="delivery-item">
            <p class="label">商家配送</p>
            <p class="value-wrapper">
              <span class="value">{{seller.deliveryPrice}}</span>
              <span class="unit">元</span>
            </p>
          </li>
          <li class="delivery-item">
            <p class="label">平均配送时间</p>
            <p class="value-wrapper">
              <span class="value">{{seller.deliveryTime}}</span>
              <span class="unit">分钟</span></p>
          </li>
        </ul>
      </div>
      <split></split>
      <div class="bulletin-wrapper">
        <h1 class="title">公告与活动</h1>
        <p class="bulletin">{{seller.bulletin}}</p>
      </div>
      <ul class="support-list">
        <li class="support-item border-height" v-for="support in seller.supports">
          <i class="icon" :class="classMap[support.type]"></i>
          <span class="text">{{support.description}}</span>
        </li>
      </ul>
      <split></split>
      <div class="pics">
        <h1 class="title">商家实景</h1>
        <div class="pic-wrapper" ref="pic-wrapper">
          <ul class="pic-list" ref="pic-list">
            <li class="pic-item" v-for="pic in seller.pics">
              <img :src="pic" width="100%" height="100%">
            </li>
          </ul>
        </div>
      </div>
      <split></split>
      <div class="basic-info">
        <h1 class="title">商家信息</h1>
        <ul class="info-list">
          <li class="info-item border-height" v-for="info in seller.infos">{{info}}</li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script type="text/ecmascript-6">
  import {saveLocalStorage, getLocalStorage} from '../../common/js/store';
  import BScroll from 'better-scroll';
  import star from '../star/star.vue';
  import split from '../split/split.vue';

  export default {
    props: {
      seller: {
        type: Object
      }
    },
    data() {
      return {
        favorite: (() => {
          return getLocalStorage(this.seller.id, 'favorite', false);
        })()
      };
    },
    computed: {
      favoriteText() {
        return this.favorite ? '已收藏' : '收藏';
      }
    },
    created() {
      this.classMap = ['decrease', 'discount', 'special', 'invoice', 'guarantee'];
      this.$nextTick(() => {
        this._initScroll();
        this._initPics();
      });
    },
    watch: {
      'seller'() {
        this._initScroll();
      }
    },
    methods: {
      toggleFavorite(event) {
        if (!event._constructed) {
          return;
        } else {
          this.favorite = !this.favorite;
          saveLocalStorage(this.seller.id, 'favorite', this.favorite);
        }
      },
      _initScroll() {
        if (!this.scroll) {
          this.scroll = new BScroll(this.$refs['seller'], {
            click: true
          });
        } else {
          this.scroll.refresh();
        }
      },
      _initPics() {
        if (this.seller.pics) {
          let picWidth = 120;
          let margin = 6;
          let width = (picWidth + margin) * this.seller.pics.length - 6;
          this.$refs['pic-list'].style.width = width + 'px';
          this.$nextTick(() => {
            if (!this.picScroll) {
              this.picScroll = new BScroll(this.$refs['pic-wrapper'], {
                scrollX: true,
                eventPassthrough: 'vertical'
              });
            } else {
              this.picScroll.refresh();
            }
          });
        }
      }
    },
    components: {
      star,
      split
    }
  };
</script>

<style lang="stylus" rel="stylesheet/stylus">
  @import "../../common/stylus/mixin.styl";
  @import "../../common/stylus/base.styl";

  .seller
    position: absolute
    left: 0
    top: 174px
    bottom: 0
    width: 100%
    overflow: hidden
    .seller-content
      .seller-top
        padding: 18px
        .seller-info
          position: relative
          padding-bottom: 18px
          border-bottom(rgba(7, 17, 27, 0.1))
          .seller-name
            title()
          .seller-desc
            font-size: 0
            .star-wrapper
              display: inline-block
              margin-right: 8px
            .rating-count
              display: inline-block
              vertical-align: top
              line-height: 18px
              margin-right: 12px
              font-size: 10px
              color: rgb(77, 85, 93)
            .seller-count
              display: inline-block
              vertical-align: top
              line-height: 18px
              font-size: 10px
              color: rgb(77, 85, 93)
          .favorite-wrapper
            position: absolute
            right: 0
            top: 0
            width: 36px
            text-align: center
            font-size: 0
            .icon-favorite
              display: block
              margin-bottom: 4px
              line-height: 24px
              font-size: 24px
              color: #d4d6d9
              &.active
                color: rgb(240, 20, 20)
            .favorite-text
              line-height: 10px
              font-size: 10px
              color: rgb(77, 85, 93)
        .delivery-list
          display: flex
          margin-top: 18px
          .delivery-item
            flex: 1
            text-align: center
            .label
              margin-bottom: 4px
              line-height: 10px
              font-size: 10px
              color: rgb(147, 153, 159)
            .value-wrapper
              line-height: 24px
              font-size: 0
              font-weight: 200
              color: rgb(7, 17, 27)
              .value
                font-size: 24px
              .unit
                font-size: 10px
          .delivery-item + .delivery-item
            border-left(rgba(7, 17, 27, 0.1))
      .bulletin-wrapper
        padding: 18px 18px 16px 18px
        .title
          title()
        .bulletin
          line-height: 24px
          padding: 0 12px
          font-size: 12px
          color: rgb(240, 20, 20)
      .support-list
        padding: 0 18px
        .support-item
          padding: 16px 12px
          border-top(rgba(7, 17, 27, 0.1))
          .icon
            display: inline-block
            vertical-align: middle
            width: 16px
            height: 16px
            margin-right: 6px
            background-size: 16px 16px
            background-repeat: no-repeat
            &.decrease
              bg-image('decrease_4')
            &.discount
              bg-image('discount_4')
            &.guarantee
              bg-image('guarantee_4')
            &.invoice
              bg-image('invoice_4')
            &.special
              bg-image('special_4')
          .text
            line-height: 16px
            font-size: 12px
            color: rgb(7, 17, 27)
      .pics
        padding: 18px
        .title
          title()
          margin-bottom: 12px
        .pic-wrapper
          width: 100%
          overflow: hidden
          white-space: nowrap
          .pic-list
            font-size: 0
            .pic-item + .pic-item
              margin-left: 6px
            .pic-item
              display: inline-block
              width: 120px
              height: 90px
      .basic-info
        padding: 18px 18px 0 18px
        .title
          title()
          margin-bottom: 12px
        .info-list
          .info-item
            padding: 16px 12px
            line-height: 16px
            border-top(rgba(7, 17, 27, 0.1))
            font-size: 12px
            color: rgb(7, 17, 27)
</style>
