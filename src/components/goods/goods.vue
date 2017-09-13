<template>
  <div class="goods">
    <div class="menu-wrapper" ref="menu-wrapper">
      <ul>
        <li class="menu-item" v-for="(item, index) in goods" :class="{'current':currentIndex === index}"
            @click="scrollTo(index,$event)">
          <span class="text border-height">
            <span class="icon" v-show="item.type>0" :class="classMap[item.type]"></span>
            {{item.name}}
        </span>
        </li>
      </ul>
    </div>
    <div class="foods-wrapper" ref="foods-wrapper">
      <ul>
        <li class="food-list food-list-hook" v-for="item in goods">
          <h1 class="title">{{item.name}}</h1>
          <ul>
            <li class="food-item border-height" v-for="food in item.foods" @click="selectFood(food, $event)">
              <div class="icon">
                <img width="57" height="57" :src="food.icon">
              </div>
              <div class="content">
                <h2 class="name">{{food.name}}</h2>
                <p class="desc">{{food.description}}</p>
                <div class="extra">
                  <span class="sellCount">月售{{food.sellCount}}份</span><span>好评率{{food.rating}}%</span>
                </div>
                <div class="price">
                  <span class="nowPrice">￥{{food.price}}</span>
                  <span class="oldPrice" v-if="food.oldPrice">￥{{food.oldPrice}}</span>
                </div>
                <div class="cartcontrol-wrapper">
                  <cartcontrol :food="food"></cartcontrol>
                </div>
              </div>
            </li>
          </ul>
        </li>
      </ul>
    </div>
    <shopcart :select-foods="selectFoods" :delivery-price="seller.deliveryPrice"
              :min-price="seller.minPrice"></shopcart>
    <food :food="selectedFood" ref="food"></food>
  </div>
</template>

<script type="text/ecmascript-6">
  import BScroll from 'better-scroll';
  import shopcart from '../../components/shopcart/shopcart';
  import cartcontrol from '../../components/cartcontrol/cartcontrol';
  import food from '../../components/food/food';

  const ERR_OK = 0;

  export default {
    props: {
      seller: {
        type: Object
      }
    },
    data(){
      return {
        goods: [],
        listHeight: [],
        scrollY: 0,
        selectedFood: {}
      };
    },
    computed: {
      currentIndex() {
        for (let i = 0; i < this.listHeight.length; i++) {
          let height1 = this.listHeight[i];
          let height2 = this.listHeight[i + 1];
          if (!height2 || this.scrollY >= height1 && this.scrollY < height2) {
            return i;
          }
        }
        return 0;
      },
      selectFoods(){
        let foods = [];
        this.goods.forEach((good) => {
          good.foods.forEach((food) => {
            if (food.count > 0) {
              foods.push(food);
            }
          });
        });
        return foods;
      }
    },
    created(){
      this.classMap = ['decrease', 'discount', 'special', 'invoice', 'guarantee'];

      this.$http.get('/api/goods').then(res => {
        res = res.body;
        if (res.errno === ERR_OK) {
          this.goods = res.data;

          this.$nextTick(() => {
            this._initScroll();
            this._calculateHeight();
          });
        } else {
          console.log('请求失败');
        }
      }, res => {
        // 请求出错
      });
    },
    methods: {
      _initScroll(){
        this.menuScroll = new BScroll(this.$refs['menu-wrapper'], {
          click: true// 取消betteer-scroll对该事件默认行为的阻止
        });

        this.foodsScroll = new BScroll(this.$refs['foods-wrapper'], {
          probeType: 3,
          click: true
        });

        this.foodsScroll.on('scroll', pos => {
          this.scrollY = Math.abs(Math.round(pos.y));
        });
      },
      _calculateHeight(){
        let foodWrapper = this.$refs['foods-wrapper'];
        let foodList = foodWrapper.getElementsByClassName('food-list-hook');
        // console.log(foodList);
        for (let i = 0; i < foodList.length; i++) {
          let item = foodList[i];
          this.listHeight.push(item.offsetTop);
        }
        // console.log(this.listHeight);
      },
      scrollTo(index, event){
        // 解决 PC端点击时事件触发两次的问题
        if (!event._constructed) {
          return;
        }
        let foodWrapper = this.$refs['foods-wrapper'];
        let foodList = foodWrapper.getElementsByClassName('food-list-hook');
        let el = foodList[index];
        this.foodsScroll.scrollToElement(el, 300);
      },
      selectFood (food, event){
        if (!event._constructed) {
          return;
        }
        this.selectedFood = food;
        this.$refs.food.show();
      }
    },
    components: {
      shopcart,
      cartcontrol,
      food
    }
  };
</script>

<style lang="stylus" rel="stylesheet/stylus">
  @import '../../common/stylus/mixin.styl'

  .goods
    position: absolute
    display: flex
    top: 174px
    bottom: 46px
    width: 100%
    overflow: hidden
    .menu-wrapper
      flex: 0 0 80
      width: 80px
      background-color: #f3f5f7
      .menu-item
        display: table
        height: 54px
        width: 56px
        line-height: 14px
        padding: 0 12px
        &.current
          position: relative
          margin-top: -1px
          z-index: 10
          background-color: #fff
          font-weight 700
          .text:after
            display: none
        .icon
          display: inline-block
          vertical-align: top
          width: 12px
          height: 12px
          margin-right: 2px
          background-size: 12px 12px
          background-repeat: no-repeat
          &.decrease
            bg-image('decrease_3')
          &.discount
            bg-image('discount_3')
          &.special
            bg-image('special_3')
          &.invoice
            bg-image('invoice_3')
          &.guarantee
            bg-image('guarantee_3')
        .text
          display: table-cell
          vertical-align: middle
          width: 56px
          border-bottom(rgba(7, 17, 27, 0.1))
          font-size: 12px
    .foods-wrapper
      flex: 1
      .food-list
        .title
          line-height: 26px
          height: 26px
          padding-left: 14px
          font-size: 12px
          border-left: 2px solid #d9dde1
          background-color: #f3f5f7
          color: rgb(147, 153, 159)
        .food-item
          display: flex
          margin: 18px
          padding-bottom: 18px
          border-bottom(rgba(7, 17, 27, 0.1))
          &:last-child
            margin-bottom: 0
          &:last-child:after
            display: none
          .icon
            flex: 0 0 57px
            margin-right: 10px
          .content
            flex: 1
            .name
              line-height: 14px
              height: 14px
              font-size: 14px
              margin-top: 2px
              color: rgb(7, 17, 27)
            .desc, .extra
              line-height: 10px
              font-size: 10px
              color: rgb(147, 153, 159)
            .desc
              line-height: 12px
              margin: 8px 0
            .extra
              .sellCount
                margin-right: 12px
            .price
              line-height: 24px
              font-weight: 700
              font-size: 0
              .nowPrice
                margin-right: 8px
                font-size: 14px
                color: rgb(240, 20, 20)
              .oldPrice
                font-size: 10px
                text-decoration line-through
                color: rgb(147, 153, 159)

            .cartcontrol-wrapper
              position: absolute
              right: -6px
              bottom: 12px
</style>
