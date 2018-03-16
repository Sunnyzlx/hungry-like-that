<template lang="pug">
  div
    div.shopcart
      div.content(@click="toggleList()")
        div.content-left
          div.logo-wrapper
            div.logo(:class="{'highlight': totalCount}")
              i.icon-shopping_cart(:class="{'highlight': totalCount}")
            div.num(v-if="totalCount") {{totalCount}}
          div.price(:class="{'highlight': totalCount}") ￥{{totalPrice}}元
          div.desc  另需配送费￥{{deliveryPrice}}元
        div.content-right(@click.stop.prevent="pay")
          div.pay(:class="payClass") {{payDesc}}
      div.ball-container
        div(v-for="ball in balls")
          transition(name="drop", @before-enter="beforeDrop", @enter="dropping", @after-enter="afterDrop")
            div.ball(v-show="ball.show")
              div.inner.inner-hook
      transition(name="fold")
        div.shopcart-list(v-show="listShow")
          div.list-header
            h1.title 购物车
            span.empty(@click="empty") 清空
          div.list-content(ref="listContent")
            ul
              li.food(v-for="(food, index) in selectFoods")
                span.name {{food.name}}
                div.price
                  span ￥{{food.price * food.count}}
                div.cartcontrol-wrapper
                  cartcontrol(:food="food")
    transition(name="fade")
      div.list-mask(v-show="listShow",@click="hideList")
</template>

<script>
import BScroll from 'better-scroll'
import cartcontrol from '@/components/cartcontrol/cartcontrol'

export default {
  name: 'shopcart',
  data() {
    return {
      fold: true,
      balls: [
        {
          show: false
        },
        {
          show: false
        },
        {
          show: false
        },
        {
          show: false
        },
        {
          show: false
        }
      ],
      dropBalls: []
    }
  },
  mounted: function() {
    var _this = this
    this.$nextTick(function() {
      _this.$root.eventHub.$on('addFood', (target) => {
        _this.drop(target)
      })
    })
  },
  methods: {
    toggleList: function() {
      if (!this.totalCount) {
        return
      }
      this.fold = !this.fold
    },
    empty: function() {
      this.selectFoods.forEach((food) => {
        food.count = 0
      })
    },
    hideList: function() {
      this.fold = true
    },
    pay: function() {
      if (this.totalPrice < this.minPrice) {
        return
      }
      window.alert(`去支付${this.totalPrice}元`)
    },
    drop(el) {
      for (let i = 0; i < this.balls.length; i++) {
        let ball = this.balls[i]
        if (!ball.show) {
          ball.show = true
          ball.el = el
          this.dropBalls.push(ball)
          return
        }
      }
    },
    beforeDrop: function(el) {
      let count = this.balls.length
      while (count--) {
        let ball = this.balls[count]
        if (ball.show) {
          let rect = ball.el.getBoundingClientRect()
          let x = rect.left - 32
          let y = -(window.innerHeight - rect.top - 22)
          el.style.display = ''
          el.style.webkitTransform = `translate3d(0, ${y}px, 0)`
          el.style.transform = `translate3d(0, ${y}px, 0)`
          let inner = el.getElementsByClassName('inner-hook')[0]
          inner.style.webkitTransform = `translate3d(${x}px, 0, 0)`
          inner.style.transform = `translate3d(${x}px, 0, 0)`
        }
      }
    },
    dropping: function(el, done) {
      /* eslint-disable no-unused-vars */
      let rf = el.offsetHeight
      this.$nextTick(() => {
        el.style.webkitTransform = 'translate3d(0, 0, 0)'
        el.style.transform = 'translate3d(0, 0, 0)'
        let inner = el.getElementsByClassName('inner-hook')[0]
        inner.style.webkitTransform = 'translate3d(0, 0, 0)'
        inner.style.transform = 'translate3d(0, 0, 0)'
        el.addEventListener('transitionend', done)
      })
    },
    afterDrop: function(el) {
      let ball = this.dropBalls.shift()
      if (ball) {
        ball.show = false
        el.style.display = 'none'
      }
    }
  },
  props: {
    selectFoods: {
      type: Array,
      default: function() {
        return [{
          'price': 6,
          'count': 2
        }]
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
  computed: {
    totalPrice: function() {
      let total = 0
      this.selectFoods.forEach((food) => {
        total += food.price * food.count
      })
      return total
    },
    totalCount: function() {
      let count = 0
      this.selectFoods.forEach((food) => {
        count += food.count
      })
      return count
    },
    payDesc() {
      if (this.totalPrice === 0) {
        return `￥${this.minPrice}元起送`
      } else if (this.totalPrice < this.minPrice) {
        let diff = this.minPrice - this.totalPrice
        return `还差￥${diff}元起送`
      } else {
        return '去结算'
      }
    },
    payClass() {
      if (this.totalPrice < this.minPrice) {
        return 'not-enough'
      } else {
        return 'enough'
      }
    },
    listShow() {
      if (!this.totalCount) {
        this.fold = true
        return false
      }
      let show = !this.fold
      if (show) {
        this.$nextTick(() => {
          if (!this.scroll) {
            this.scroll = new BScroll(this.$refs.listContent, {
              click: true
            })
          } else {
            this.scroll.refresh()
          }
        })
      }
      return show
    }
  },
  components: {
    cartcontrol
  }
}
</script>

<style lang="stylus">
   @import "../../common/stylus/mixin.styl"
   .shopcart
     position: fixed
     left: 0
     bottom: 0
     width: 100%
     height: 48px
     z-index 50
     .content
       display flex
       background #141d27  
       fon-size 0
       color rgba(255, 255, 255, 0.4)
       .content-left
         flex: 1
         .logo-wrapper
           display inline-block
           position relative
           top -10px
           margin 0 12px
           padding 6px
           width 56px
           height 56px
           box-sizing border-box
           vertical-align top
           border-radius 50%
           background #141d27
           .logo
             width 100%
             height 100%
             border-radius 50%
             text-align center
             background #2b343c
             &.highlight
               background rgb(0, 160, 220)
             .icon-shopping_cart
               font-size 24px
               line-height 44px
               color #80858a
               &.highlight
                color #fff
           .num
             position absolute
             top 0
             right 0
             width 24px
             height 16px
             line-height 16px
             text-align center
             border-radius 16px
             font-size 9px
             font-weight 700
             color #fff
             background rgb(240, 20, 20)
             box-shadow 0 4px 8px 0 rgba(0, 0, 0, 0.4)
         .price
           display inline-block
           vertical-align top
           margin-top 12px
           line-height 24px
           padding-right 12px 
           border-right 1px solid rgba(255, 255, 255, 0.1)
           box-sizing border-box
           font-size 16px
           font-weight 700
           &.highlight
             color #fff
         .desc
           display inline-block
           vertical-align top
           margin 12px 0 0 12px
           line-height 24px       
           font-size 10px
       .content-right
         flex 0 0 105px
         width 105px
         .pay
           height 48px
           line-height 48px
           text-align center
           font-size 12px
           font-weight 700
           &.not-enough
             background #2b333b
           &.enough
             background #00b43c
             color #fff
     .ball-container
       .ball
         position fixed
         left 32px
         bottom 22px
         z-index 200
         transition all .4s cubic-bezier(0.49, -0.29, 0.75, 0.41)
         .inner
           width 16px
           height 16px
           border-radius 50%
           background rgb(0, 160, 220)
           transition all .4s linear
     .shopcart-list
       position absolute
       top 0
       left 0
       z-index -1
       width 100%
       background #fff
       transform translate3d(0, -100%, 0)
       &.fold-enter-active, &.&.fold-leave-active
         transition all 0.5s
       &.fold-enter, &.&.fold-leave-to
         transform translate3d(0, 0, 0)
       .list-header
         position relative
         height 40px
         line-height 40px
         padding 0 18px
         background #f3f5f7
         border-bottom 1px solid rgba(7, 17, 27, 0.1)
         .title
           float left
           font-size 14px
           color rgb(7, 17, 27)
         .empty
           position absolute
           right 18px
           font-size 12px
           color rgb(0, 160, 220)
       .list-content
         padding 0 18px
         max-height 217px
         background #fff
         overflow hidden
         .food
           position relative
           padding 12px 0
           box-sizing border-box
           border-1px-bottom(rgba(7, 17, 27, 0.1))
           .name
             line-height 24px
             font-size 14px
             color rgb(7, 17, 27)
           .price
             position absolute
             right 90px
             bottom 12px
             line-height 24px
             font-size 14px
             font-weight 700
             color rgb(240, 20, 20)
           .cartcontrol-wrapper
             position absolute
             right 0
             bottom 6px
   .list-mask
     position fixed
     top 0
     left 0
     width 100%
     height 100%
     z-index 40
     opacity 1
     background rgba(7, 17, 27, 0.6)
     &.fade-enter-active, &.fade-leave-active
       transition all 0.5s
     &.fade-enter, &.fade-leave-to
       opacity 0
       background rgba(7,17, 27, 0)
</style>