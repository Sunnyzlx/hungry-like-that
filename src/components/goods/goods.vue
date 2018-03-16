<template>
  <div>
    <div class="goods">
      <div class="menu-wrapper" ref="menu">
        <ul>
          <li v-for="(item,index) in goods" :key="index" class="menu-item border-1px-bottom" :class="{'current': currentIndex === index}" @click='selectIndex(index,$event)'>
            <span class="text">
              <span class="icon" v-if="item.type>0" :class="classMap[index]"></span>{{item.name}}</span>
          </li>
        </ul>
      </div>
      <div class="foods-wrapper" ref="foods">
        <ul>
          <li v-for="(item,index) in goods" :key="index" class="food-list food-list-hook">
            <h1 class="title">{{item.name}}</h1>
            <ul>
              <li class="food-item border-1px-bottom" @click="selectFood(food, $event)" v-for="(food,index) in item.foods" :key="index">
                <div class="icon">
                  <img :src="food.icon" alt="food" width="57" height="57">
                </div>
                <div class="content">
                  <h2 class="name">{{food.name}}</h2>
                  <p class="desc">{{food.description}}</p>
                  <div class="extra">
                    <span class="count">月售{{food.sellCount}}份</span>
                    <span>好评率{{food.rating}}%</span>
                  </div>
                  <div class="price">
                    <span class="now">￥{{food.price}}</span>
                    <span class="old" v-show="food.oldPrice">￥{{food.oldPrice}}</span>
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
      <shopcart :select-foods="selectFoods" :delivery-price="seller.deliveryPrice" :min-price="seller.minPrice"></shopcart>
    </div>
    <food :food="selectedFood" ref="food"></food>
  </div>
</template>

<script>
import BScroll from 'better-scroll'
import shopcart from '@/components/shopcart/shopcart'
import cartcontrol from '@/components/cartcontrol/cartcontrol'
import food from '@/components/food/food'
const ERR_OK = 0

export default {
  name: 'goods',
  data() {
    return {
      goods: [],
      listHeight: [],
      foodsList: [],
      scrollY: 0,
      selectedFood: {}
    }
  },
  components: {
    shopcart,
    cartcontrol,
    food
  },
  props: {
    seller: {}
  },
  created() {
    this.classMap = ['decrease', 'discount', 'special', 'invoice', 'guarantee']
    this.$http.get('/api/goods').then((res) => {
      res = res.body
      if (res.errno === ERR_OK) {
        this.goods = res.data
        this.$nextTick(() => {
          this._initScroll()
          this._calculateHeight()
        })
      }
    })
  },
  computed: {
    currentIndex: function() {
      for (let i = 0; i < this.listHeight.length; i++) {
        let height1 = this.listHeight[i]
        let height2 = this.listHeight[ i + 1 ]
        if (!height2 || (this.scrollY >= height1 && this.scrollY < height2)) {
          return i
        }
      }
      return 0
    },
    selectFoods() {
      let foods = []
      this.goods.forEach((good) => {
        good.foods.forEach((food) => {
          if (food.count) {
            foods.push(food)
          }
        })
      })
      return foods
    }
  },
  methods: {
    selectIndex(index, event) {
      if (!event._constructed) {
        return
      }
      let foodsList = this.$refs.foods.getElementsByClassName('food-list-hook')
      let el = foodsList[index]
      this.foodsScroll.scrollToElement(el, 300)
    },
    selectFood(food, event) {
      if (!event._constructed) {
        return
      }
      this.selectedFood = food
      this.$refs.food.show()
    },
    _initScroll() {
      this.menuScroll = new BScroll(this.$refs.menu, {
        click: true
      })
      this.foodsScroll = new BScroll(this.$refs.foods, {
        click: true,
        probeType: 3
      })
      this.foodsScroll.on('scroll', (pos) => {
        this.scrollY = Math.abs(Math.round(pos.y))
      })
    },
    _calculateHeight() {
      let foodsList = this.$refs.foods.getElementsByClassName('food-list-hook')
      let height = 0
      this.listHeight.push(height)
      for (let i = 0; i < foodsList.length; i++) {
        let item = foodsList[i].clientHeight
        height += item
        this.listHeight.push(height)
      }
    }
  }
}
</script>

<style lang="stylus" type="stylesheet/stylus">
  @import '../../common/stylus/mixin'

  .goods
    display: flex
    position: absolute
    top: 174px
    bottom: 46px
    width: 100%
    overflow: hidden
    .menu-wrapper
      flex: 0 0 80px
      width: 80px
      background: #f3f5f7
      .menu-item
        display: table
        width: 56px
        height: 54px
        line-height: 14px
        padding 0 12px
        border-1px-bottom(rgba(7, 17, 27, 0.1))
        &.current
          position relative
          z-index 10
          font-weight 700
          background #fff
          margin-top -1px
          .text
            border-none()
        .text
          display: table-cell
          vertical-align: middle
          font-size: 12px
          .icon
            display: inline-block
            vertical-align: top
            width: 12px
            height: 12px
            background-size: 12px 12px
            background-repeat: no-repeat
            margin-right: 2px
            &.decrease
              bg-image('decrease_3')
            &.discount
              bg-image('discount_3')
            &.guarantee
              bg-image('guarantee_3')
            &.invoice
              bg-image('invoice_3')
            &.special
              bg-image('special_3')
    .foods-wrapper
      flex: 1
      .title
        width: 100%
        height: 26px
        padding-left: 14px
        line-height: 26px
        background: #f3f5f7
        border-left: 2px solid #d9dde1
        font-size: 12px
        color: rgb(147, 153, 159)
      .food-item
        display: flex
        margin: 0 18px
        padding: 18px 0
        border-1px-bottom(rgba(7, 17, 27, 0.1))
        &:last-child
          border-none()
        .icon
          flex: 0 0 57px
          width: 57px
          margin-right: 10px
        .content
          flex: 1
          .name
            margin-top: 2px
            font-size: 14px
            line-height: 14px
            color: rgb(7, 17, 27)
          .desc, .extra
            margin-top: 8px
            font-size: 10px
            line-height: 12px
            color: rgb(147, 153, 159)
          .extra
            .count
              margin-right: 12px
          .price
            margin-top: 8px
            .now
              font-size: 14px
              font-weight: 700
              margin-right: 8px
              color: rgb(240, 20, 20)
            .old
              text-decoration: line-through
              font-size: 10px
              font-weight: 700
              color: rgb(147, 153, 159)
          .cartcontrol-wrapper
            position absolute
            right 0
            bottom 12px
</style>
