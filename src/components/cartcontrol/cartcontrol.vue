<template>
  <div class="cartcontrol">
    <transition name="move">
      <div class="cart-decrease icon-remove_circle_outline" v-show="food.count>0" @click.stop="decreaseCart"></div>
    </transition>
    <transition name="move">
      <div class="cart-count" v-show="food.count>0">{{food.count}}</div>
    </transition>
    <div class="cart-add icon-add_circle" @click.stop="addCart"></div>
  </div>
</template>

<script type="text/ecmascript-6">
  export default{
    props: {
      food: {
        type: Object
      }
    },
    methods: {
      addCart(e){
        if (!e._constructed) {
          return;
        }
        if (!this.food.count) {
          this.$set(this.food, 'count', 1);
        } else {
          this.food.count++;
        }
      },
      decreaseCart(e){
        if (!e._constructed) {
          return;
        }
        this.food.count--;
      }
    }
  };
</script>

<style lang="stylus" rel="stylesheet/stylus">
  .cartcontrol
    font-size: 0
    .cart-decrease
      display: inline-block
      padding: 6px
      line-height: 24px
      font-size: 24px
      color: rgb(0, 160, 220)
      transition: all 0.2s linear
      &.move-enter-active, &.move-leave-active
        opacity: 1
        transform: translate3D(0, 0, 0) rotateZ(0)
      &.move-enter, &.move-leave-active
        opacity: 0
        transform: translate3D(24px, 0, 0) rotateZ(-180deg)
    .cart-count
      display: inline-block
      vertical-align: top
      line-height: 24px
      padding-top: 6px
      width: 12px
      text-align: center
      font-size: 10px
      color: rgb(147, 153, 159)
    .cart-add
      display: inline-block
      padding: 6px
      line-height: 24px
      font-size: 24px
      color: rgb(0, 160, 220)
</style>
