<template>
  <div class="shopcart">
    <div class="content">
      <div class="content-left">
        <div class="logo-wrapper" @click="toggleList">
          <div class="logo" :class="{'highlight':totalCount>0}">
            <i class="icon-shopping_cart"></i>
          </div>
          <div class="num" v-show="totalCount>0">{{totalCount}}</div>
        </div>
        <div class="price" :class="{'highlight':totalPrice>0}">￥{{totalPrice}}</div>
        <div class="desc">另需配送费￥{{deliveryPrice}}元</div>
      </div>
      <div class="content-right" @click="pay">
        <div class="pay" :class="payClass">{{payDesc}}</div>
      </div>
    </div>
    <transition name="fold">
      <div class="shopcart-list" v-show="listShow">
        <div class="list-header">
          <h1 class="title">购物车</h1>
          <span class="empty" @click="empty">清空</span>
        </div>
        <div class="list-content" ref="list-content">
          <ul>
            <li class="food" v-for="food in selectFoods">
              <span class="name">{{food.name}}</span>
              <div class="price">
                <span>￥{{food.price * food.count}}</span>
              </div>
              <div class="cartcontrol-wrapper">
                <cartcontrol :food="food"></cartcontrol>
              </div>
            </li>
          </ul>
        </div>
      </div>
    </transition>
    <transition name="mask-fade">
      <div class="list-mask" v-show="listShow" @click="hideList"></div>
    </transition>
  </div>
</template>

<script type="text/ecmascript-6">
  import BScroll from 'better-scroll';
  import cartcontrol from '../cartcontrol/cartcontrol';

  export default {
    props: {
      selectFoods: {
        type: Array,
        default() {
          return [
            {
              price: 0,
              count: 0
            }
          ];
        }
      },
      deliveryPrice: {
        type: Number,
        default: 0
      },
      minPrice: {
        type: Number,
        default: 0
      }
    },
    data() {
      return {
        balls: [
          {
            show: false
          }, {
            show: false
          }, {
            show: false
          }, {
            show: false
          }, {
            show: false
          }
        ],
        fold: true
      };
    },
    computed: {
      totalPrice() {
        let total = 0;
        this.selectFoods.forEach(food => {
          total += food.price * food.count;
        });
        return total;
      },
      totalCount() {
        let count = 0;
        this.selectFoods.forEach(food => {
          count += food.count;
        });
        return count;
      },
      payDesc() {
        if (this.totalPrice === 0) {
          return `${this.minPrice}元起送`;
        } else if (this.totalPrice < this.minPrice) {
          return `还差${this.minPrice - this.totalPrice}元起送`;
        } else {
          return '去结算';
        }
      },
      payClass() {
        if (this.totalPrice < this.minPrice) {
          return 'not-enough';
        } else {
          return 'enough';
        }
      },
      listShow() {
        if (!this.totalCount) {
          this.fold = true;
          return false;
        }

        let show = !this.fold;

        // 初始化better-scroll
        if (show) {
          this.$nextTick(() => {
            if (!this.scroll) {
              this.scroll = new BScroll(this.$refs['list-content'], {
                click: true
              });
            } else {
              this.scroll.refresh();
            }
          });
        }
        return show;
      }
    },
    methods: {
      toggleList() {
        if (!this.totalCount) {
          return;
        }
        this.fold = !this.fold;
      },
      hideList() {
        this.fold = true;
      },
      empty() {
        this.selectFoods.forEach((food) => {
          food.count = 0;
        });
      },
      pay() {
        if (this.totalPrice < this.minPrice) {
          return;
        } else {
          alert(`去支付${this.totalPrice}元`);
        }
      }
    },
    components: {
      cartcontrol
    }
  };
</script>

<style lang="stylus" rel="stylesheet/stylus">
  @import '../../common/stylus/mixin.styl'

  .shopcart
    position: fixed
    left: 0
    bottom: 0
    z-index: 10
    width: 100%
    height: 48px
    .content
      display: flex
      position: relative
      height: 100%
      background-color: #141d27
      color: rgba(255, 255, 255, 0.4)
      z-index: 11
      .content-left
        flex: 1
        .logo-wrapper
          display: inline-block
          vertical-align: top
          position: relative
          top: -10px
          margin: 0 12px
          padding: 6px
          width: 56px
          height: 56px
          box-sizing: border-box
          border-radius: 50%
          background-color: #141d27
          .logo
            line-height: 48px
            width: 100%
            height: 100%
            border-radius: 50%
            background-color: #2b343c
            text-align: center
            font-size: 24px
            &.highlight
              background-color: rgb(0, 160, 220)
              color: #ffffff
          .num
            display: inline-block
            position: absolute
            right: 0
            top: 0
            width: 24px
            height: 16px
            line-height: 16px
            text-align: center
            font-size: 9px
            font-weight: 700
            background-color: rgb(240, 20, 20)
            box-shadow: 0 4px 8px 0 rgba(0, 0, 0, 0.4)
            border-radius: 16px
            color: #ffffff
        .price
          display: inline-block
          vertical-align: top
          line-height: 24px
          margin-top: 12px
          padding-right: 12px
          box-sizing: border-box
          border-right: 1px solid rgba(255, 255, 255, 0.1)
          font-size: 16px
          font-weight: 700
          &.highlight
            color: #ffffff
        .desc
          display: inline-block
          vertical-align: top
          margin: 12px 0 0 12px
          line-height: 24px
          font-size: 10px
          fong-weight: 700
      .content-right
        flex: 0 0 105px
        width: 105px
        .pay
          height: 48px
          line-height: 48px
          text-align: center
          font-size: 12px
          font-weight: 700
          &.not-enough
            background-color: #2b333b
          &.enough
            background-color: #00b43c
            color: #ffffff

    .fold-enter-active
      animation: fold-in 300ms
    .fold-leave-active
      animation: fold-in 300ms reverse
      @keyframes fold-in
          0%
            transform: translateY(0)
          25%
            transform: translateY(-25%)
          50%
            transform: translateY(-50%)
          75%
            transform: translateY(-75%)
          100%
            transform: translateY(-100%)
    .shopcart-list
      position: absolute
      left: 0
      top:0
      transform :translateY(-100%)
      width: 100%
      z-index: 10
      .list-header
        height: 40px
        line-height: 40px
        padding: 0 18px
        background-color: #f3f5f7
        border-bottom: 1px solid rgba(7, 17, 27, 0.1)
        .title
          float: left
          line-height: 40px
          font-size: 14px
          font-weight: 200
          color: rgb(7, 17, 27)
        .empty
          float: right
          line-height: 40px
          font-size: 12px
          color: rgb(0, 160, 220)
      .list-content
        padding: 0 18px
        max-height: 217px
        overflow: hidden
        background-color: #ffffff;
        .food
          position: relative
          box-sizing: border-box
          height: 48px
          padding: 12px 0
          border-1px(rgba(7, 17, 27, 0.1))
          .name
            line-height: 24px
            font-size: 14px
            color: rgb(7, 17, 27)
          .price
            position: absolute
            right: 84px
            top: 50%
            transform: translateY(-50%)
            margin: 0 12px 0 18px
            font-size: 14px
            font-weight: 700
            color: rgb(240, 20, 20)
          .cartcontrol-wrapper
            position: absolute
            right: -6px
            top: 50%
            transform: translateY(-50%)

    .mask-fade-enter-active,.mask-fade-leave-active
      transition: all ease 300ms
    .mask-fade-enter,.mask-fade-leave-to
      opacity:0
      background-color: rgba(7, 17, 27, 0)
    .list-mask
      position: fixed
      top: 0
      left: 0
      width: 100%
      height: 100%
      z-index: 9
      backdrop-filter: blur(10px)
      background-color: rgba(7, 17, 27, 0.6)
</style>
