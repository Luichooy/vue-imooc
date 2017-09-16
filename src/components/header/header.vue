<template>
  <div class="header">
    <div class="content-wrapper">
      <div class="avatar">
        <img alt="商家头像" width="64" height="64" :src="seller.avatar">
      </div>
      <div class="content">
        <div class="title">
          <span class="brand"></span>
          <span class="name">{{seller.name}}</span>
        </div>
        <div class="description">
          {{seller.description}}/{{seller.deliveryTime}}分钟送达
        </div>
        <div class="supports" v-if="seller.supports">
          <span class="icon" :class="classMap[seller.supports[0].type]"></span>
          <span class="text">{{seller.supports[0].description}}</span>
        </div>
      </div>
      <div class="supports-count" v-if="seller.supports" @click="showDetail">
        <span class="count">{{seller.supports.length}}个</span>
        <i class="icon-keyboard_arrow_right"></i>
      </div>
    </div>
    <div class="bulletin-wrapper" @click="showDetail">
      <span class="bulletin-title"></span><span class="bulletin-text">{{seller.bulletin}}</span>
      <i class="icon-keyboard_arrow_right"></i>
    </div>
    <div class="background">
      <img width="100%" height="100%" :src="seller.avatar">
    </div>
    <transition name="fade">
      <div class="detail" v-show="detailShow">
        <div class="detail-wrapper clearfix">
          <div class="detail-main">
            <h1 class="name">{{seller.name}}</h1>
            <div class="star-wrapper">
              <star :size="48" :score="seller.score"></star>
            </div>
            <div class="title">
              <div class="line"></div>
              <div class="text">优惠信息</div>
              <div class="line"></div>
            </div>
            <ul class="supports" v-if="seller.supports">
              <li class="support-item" v-for="item in seller.supports">
                <span class="icon" :class="classMap[item.type]"></span>
                <span class="text">{{item.description}}</span>
              </li>
            </ul>
            <div class="title">
              <div class="line"></div>
              <div class="text">商家公告</div>
              <div class="line"></div>
            </div>
            <div class="bulletin">
              <p class="content">{{seller.bulletin}}</p>
            </div>
          </div>
        </div>
        <div class="detail-close" @click="hideDetail">
          <i class="icon-close"></i>
        </div>
      </div>
    </transition>
  </div>
</template>

<script type="text/ecmascript-6">
  import star from '../star/star';

  export default {
    props: {
      seller: {
        type: Object
      }
    },
    data(){
      return {
        detailShow: false
      };
    },
    methods: {
      showDetail(){
        this.detailShow = true;
      },
      hideDetail(){
        this.detailShow = false;
      }
    },
    created(){
      this.classMap = ['decrease', 'discount', 'special', 'invoice', 'guarantee'];
    },
    components: {
      star
    }
  };
</script>

<style lang="stylus" rel="stylesheet/stylus">
  @import "../../common/stylus/mixin.styl"
  .header
    position: relative
    overflow: hidden
    color: #ffffff
    background-color: rgba(7, 17, 27, 0.5)
    .content-wrapper
      position: relative
      padding: 24px 12px 18px 24px
      font-size: 0
      .avatar
        display: inline-block
        vertical-align: top
        margin-right: 16px
        img
          border-radius: 2px
      .content
        display: inline-block
        .title
          margin: 2px 0 8px 0
          .brand
            display: inline-block
            vertical-align: top
            width: 30px
            height: 18px
            bg-image("brand")
            background-size: 30px 18px
            background-repeat: no-repeat
          .name
            margin-left: 6px
            font-size: 16px
            font-weight: bold
            line-height: 18px
        .description
          margin-bottom: 10px
          font-size: 12px
          line-height: 12px
        .supports
          .icon
            display: inline-block
            vertical-align: top
            width: 12px
            height: 12px
            margin-right: 4px
            background-size: 12px 12px
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
            position:relative
            top: 1px
            line-height: 12px
            font-size: 10px
      .supports-count
        position: absolute
        right: 12px
        bottom: 14px
        padding: 0 8px
        height: 24px
        line-height: 24px
        border-radius: 14px
        background-color: rgba(0, 0, 0, 0.2)
        text-align: center
        .count
          vertical-align: top
          font-size: 10px
        .icon-keyboard_arrow_right
          margin-left: 2px
          line-height: 24px
          font-size: 10px

    .bulletin-wrapper
      position: relative
      height: 28px
      line-height: 28px
      padding: 0 22px 0 12px
      background-color: rgba(7, 17, 27, 0.2)
      white-space: nowrap
      overflow: hidden
      text-overflow: ellipsis
      /*font-size: 0*/
      .bulletin-title
        display: inline-block
        margin-top: 8px
        width: 22px
        height: 12px
        bg-image("bulletin")
        background-size: 22px 12px
        background-repeat: no-repeat
      .bulletin-text
        vertical-align: top
        margin: 0 4px
        font-size: 10px
      .icon-keyboard_arrow_right
        position: absolute
        right: 12px
        bottom: 9px
        font-size: 10px
    .background
      position: absolute
      top: 0
      left: 0
      width: 100%
      height: 100%
      z-index: -1
      filter: blur(10px)
    .fade-enter-active,.fade-leave-active
      transition: all linear 300ms
    .fade-enter,.fade-leave-to
      opacity: 0
      background:rgba(7, 17, 27, 0)
    .detail
      position: fixed
      top: 0
      left: 0
      width: 100%
      height: 100%
      z-index: 100
      overflow: auto
      background: rgba(7, 17, 27, 0.8)
      transition:all 0.5s linear
      backdrop-filter:blur(10px)// 浮层背景模糊，只在iphone上有效
      .detail-wrapper
        width: 100%
        min-height: 100%
        .detail-main
          margin-top: 64px
          padding-bottom: 64px
          .name
            line-height: 16px
            text-align: center
            font-size: 16px
            font-weight: 700
          .star-wrapper
            margin: 16px auto 28px auto
            text-align: center
          .title
            display:flex
            width:80%
            margin:0 auto 24px auto;
            .line
              flex:1
              position:relative
              top: -6px
              border-bottom:1px solid rgba(255,255,255,0.2)
            .text
              line-height: 14px
              padding:0 12px
              font-size: 14px
              font-weight:700
          .supports
            width:80%
            margin:0 auto 28px auto
            .support-item
              padding:0 12px
              margin-bottom: 12px
              font-size:0
              &:last-child
                margin-bottom:0
              .icon
                display:inline-block
                vertical-align: top
                width: 16px
                height: 16px
                margin-right: 6px
                background-size:16px 16px
                background-repeat:no-repeat
                &.decrease
                  bg-image('decrease_2')
                &.discount
                  bg-image('discount_2')
                &.guarantee
                  bg-image('guarantee_2')
                &.invoice
                  bg-image('invoice_2')
                &.special
                  bg-image('special_2')
              .text
                line-height: 16px
                font-size: 12px
          .bulletin
            width:80%
            margin:0 auto
            .content
              line-height: 24px
              padding:0 12px
              font-size: 12px
              text-align:justify-self start
      .detail-close
        position: relative
        width: 32px
        height: 32px
        margin: -64px auto 0 auto
        clear: both
        font-size: 32px
        color: rgba(255, 255, 255, 0.5)
</style>
