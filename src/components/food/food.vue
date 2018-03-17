<template lang="pug">
  transition(name="move")
    div.food(v-show="showFlag",ref="food")
      div.food-content
        div.image-header
          img(:src="food.image")
          div.back(@click="hide")
            i.icon-arrow_lift
        div.content
          h1.title {{food.name}}
          div.detail
            span.sell-count 月售{{food.sellCount}}份
            span.rating 好评率{{food.rating}}%
          div.price
            span.now ￥{{food.price}}
            span.old(v-show="food.oldPrice") ￥{{food.oldPrice}}
          div.cartcontrol-wrapper
            cartcontrol(:food="food", v-show="food.count")
          transition(name="fade")
            div.buy(v-show="!food.count || food.count===0", @click.stop.prevent="addFirst($event)") 加入购物车
        split(v-show="food.info")
        div.info(v-show="food.info")
          h1.title 商品信息
          p.text {{food.info}}
        split
        div.rating
          h1.title 商品评价
          ratingselect(:rating="food.ratings", :select-type="selectType", :only-content="onlyContent", :desc="desc")
          div.rating-wrapper
            ul(v-show="food.ratings && food.ratings.length")
              li.rating-item.border-1px-bottom(v-show="needShow(rating.rateType, rating.text)",v-for="rating in food.ratings")
                div.user
                  span.name {{rating.username}}
                  img.avatar(width="12", height="12", :src="rating.avatar")
                div.time {{rating.rateTime}}
                p.text
                  span(:class="{'icon-thumb_up':rating.rateType===0, 'icon-thumb_down':rating.rateType===1}")
                  span {{rating.text}}
            div.no-rating(v-show="!food.ratings || !food.ratings")
</template>

<script>
import BScroll from 'better-scroll'
import cartcontrol from '@/components/cartcontrol/cartcontrol'
import split from '@/components/split/split'
import ratingselect from '@/components/ratingselect/ratingselect'
// const POSITIVE = 0
// const NEGATIVE = 1
const ALL = 2

export default {
  name: 'food',
  data() {
    return {
      showFlag: false,
      selectType: ALL,
      onlyContent: true,
      desc: {
        all: '全部',
        positive: '推荐',
        negative: '吐槽'
      }
    }
  },
  props: {
    food: {
      type: Object
    }
  },
  methods: {
    show: function() {
      this.showFlag = true
      this.selectType = ALL
      this.onlyContent = true
      this.$nextTick(() => {
        if (!this.scroll) {
          this.scroll = new BScroll(this.$refs.food, {
            click: true
          })
        } else {
          this.scroll.refresh()
        }
      })
    },
    hide: function() {
      this.showFlag = false
    },
    addFirst: function(event) {
      if (!event._constructed) {
        return
      }
      this.$set(this.food, 'count', 1)
    },
    needShow(type, text) {
      if (this.onlyContent && !text) {
        return false
      }
      if (this.selectType === ALL) {
        return true
      } else {
        return type === this.selectType
      }
    }
  },
  components: {
    cartcontrol,
    split,
    ratingselect
  }
}
</script>


<style lang="stylus">
@import "../../common/stylus/mixin.styl"

  .food
    position fixed
    left 0
    top 0
    bottom 48px
    z-index 30
    width 100%
    background #fff
    transform translate3d(0, 0, 0)
    &.move-enter-active, &.move-leave-active
      transition all 0.2s linear
    &.move-enter, &.move-leave-to
      transform translate3d(100%, 0, 0)
    .image-header
      position relative
      width 100%
      height 0
      padding-top 100%
      img
        position absolute
        left 0
        top 0
        width 100%
        height 100%
      .back
        position absolute
        top 10px
        left 0
        .icon-arrow_lift
          display block
          padding 10px
          font-size 20px
          color #fff
    .content
      position relative
      padding 18px
      .title
        line-height 14px
        margin-bottom 8px
        font-size 14px
        font-weight 700
        color rgb(7, 17, 27)
      .detail
        margin-bottom 18px
        line-height 10px
        height 10px
        font-size 0
        .sell-count, .rating
          font-size 10px
          color rgb(147, 153, 159)
        .sell-count
          margin-right 12px
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
        right 12px
        bottom 12px
      .buy
        position absolute
        right 18px
        bottom 18px
        z-index 10
        height 24px
        line-height 24px
        padding 0 12px
        box-sizing border-box
        border-radius 12px
        font-size 10px
        color #fff
        background rgb(0, 160, 220)
        opacity 1
        &.fade-enter-active, &.fade-leave-active
          transition all 0.2s
        &.fade-enter, &.fade-leave-to
          opacity 0
    .info
      padding 18px
      .title
        line-height 14px
        margin-bottom 6px
        font-size 14px
        color rgb(7, 17, 27)
      .text
        line-height 24px
        padding 0 8px
        font-size 12px
        color #07111b
    .rating
      padding-top 18px
      .title
        line-height 14px
        margin-left 18px
        font-size 14px
        color rgb(7, 17, 27)
      .rating-wrapper
        padding 0 18px
        .rating-item
          position relative
          padding 16px 0
          border-1px-bottom(rgba(7, 17, 27, 0.1))
          .user
            position absolute
            right 0
            top 16px
            line-height 12px
            font-size 0
            .name
              display inline-block
              margin-right 6px
              vertical-align top
              font-size 10px
              color rgb(147, 153, 159)
            .avatar
              border-radius 50%
          .time
            margin-bottom 6px
            line-height 12px
            font-size 10px
            color rgb(147, 153, 159)
          .text
            line-height 16px
            font-size 12px
            color rgb(7, 17, 27)
            .icon-thumb_up, .icon-thumb_down
              margin-right 4px
              line-height 16px
              font-size 12px
            .icon-thumb_up
              color rgb(0, 160, 220)  
            .icon-thumb_down
              color rgb(147, 153, 159)
</style>
