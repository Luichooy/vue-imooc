<template>
  <div class="seller">
    <div class="seller-content" ref="seller-content">
      <div class="seller-top">
        <div class="seller-info border-height">
          <h1 class="seller-name">{{seller.name}}</h1>
          <div class="seller-desc">
            <div class="star-wrapper">
              <star :size="36" :score="seller.score"></star>
            </div>
            <span class="seller-count">月售690单</span>
          </div>
          <div class="collection-wrapper">
            <div class="collection-icon">
              <i class="icon icon-favorite"></i>
            </div>
            <p class="collection-text">已收藏</p>
          </div>
        </div>
        <ul class="delivery-list">
          <li class="delivery-item">
            <p class="label">起送价</p>
            <p class="value-wrapper">
              <span class="value">20</span>
              <span class="unit">元</span>
            </p>
          </li>
          <li class="delivery-item">
            <p class="label">商家配送</p>
            <p class="value-wrapper">
              <span class="value">20</span>
              <span class="unit">元</span>
            </p>
          </li>
          <li class="delivery-item">
            <p class="label">平均配送时间</p>
            <p class="value-wrapper">
              <span class="value">20</span>
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
      <div class="imgage-wrapper">
        <h1 class="title">商家实景</h1>
        <ul class="image-list clearfix">
          <li class="image-item" v-for="pic in seller.pics">
            <img :src="pic" alt="">
          </li>
        </ul>
      </div>
      <split></split>
      <div class="basic-info">
        <h1 class="title">商家信息</h1>
        <ul class="info-list">
          <li class="info-item border-height" v-for="info in seller.infos">{{info}}</li>
        </ul>
      </div>
    </div>
    <shopcart></shopcart>
  </div>
</template>

<script type="text/ecmascript-6">
  import star from '../star/star.vue';
  import split from '../split/split.vue';
  import shopcart from '../shopcart/shopcart.vue';
  import BScroll from 'better-scroll';
  export default {
    props: {
      seller: {
        type: Object
      }
    },
    created(){
      this.classMap = ['decrease', 'discount', 'special', 'invoice', 'guarantee'];
      this._initScroll();
    },
    methods: {
      _initScroll() {
        console.log(this.$refs);
        this.contentScroll = new BScroll(this.$refs['seller-content'], {
          click: true
        });
        console.log('_initScroll');
      }
    },
    components: {
      star,
      split,
      shopcart
    }
  };
</script>

<style lang="stylus" rel="stylesheet/stylus">
  @import "../../common/stylus/mixin.styl";
  @import "../../common/stylus/base.styl";

  .seller
    .seller-top
      padding: 18px
      .seller-info
        position: relative
        padding-bottom: 18px
        border-bottom(rgba(7,17,27,0.1))
        .seller-name
          title()
        .seller-desc
          font-size: 0
          .star-wrapper
            display: inline-block
            margin-right: 24px
          .seller-count
            display: inline-block
            vertical-align: top
            line-height: 18px
            font-size: 10px
            color: rgb(77,85,93)
        .collection-wrapper
          position: absolute
          right: 0
          top: 0
          .collection-icon
            margin-bottom: 4px
            text-align:center
            .icon
              line-height: 24px
              font-size: 24px
              color: rgb(240,20,20)
          .collection-text
            line-height: 10px
            font-size: 10px
            text-align: center
            color: rgb(77,85,93)
      .delivery-list
        margin-top: 18px
        display: flex
        justify-content: space-between
        text-align: center
        .delivery-item
          width: 33.33%
          .label
            margin-bottom: 4px
            line-height: 10px
            font-size: 10px
            color: rgb(147,153,159)
          .value-wrapper
            line-height: 24px
            font-size: 0
            font-weight: 200
            color: rgb(7,17,27)
            .value
              font-size: 24px
            .unit
              font-size: 10px
        .delivery-item + .delivery-item
          border-left(rgba(7,17,27,0.1))
    .bulletin-wrapper
      padding: 18px 18px 12px 18px
      .title
        title()
      .bulletin
        line-height: 24px
        padding: 0 12px
        font-size: 12px
        font-weight: 200
        color: rgb(240,20,20)
    .support-list
      padding: 18px
      .support-item
        padding: 16px 12px
        border-top(rgba(7,17,27,0.1))
        .icon
          display: inline-block
          vertical-align: middle
          width: 16px
          height: 16px
          margin-right: 6px
          background-size: 16px 16px
          background-repeat: no-repeat
          &.decrease
            bg-image('decrease_1')
          &.discount
            bg-image('discount_1')
          &.guarantee
            bg-image('guarantee_1')
          &.invoice
            bg-image('invoice_1')
          &.special
            bg-image('special_1')
        .text
          line-height: 16px
          font-size: 12px
          font-weight: 200
          color: rgb(7,17,27)
    .imgage-wrapper
      padding: 18px 0 18px 18px
      .title
        title()
        margin-bottom: 12px
      .image-list
        width: 100%
        height: 90px
        overflow: hidden
        .image-item
          float: left
        .image-item + .image-item
            margin-left: 6px
          img
            display: block
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
          lint-height: 16px
          border-top(rgba(7,17,27,0.1))
          font-size: 12px
          font-weight: 200
          color: rgb(7,17,27)
</style>
