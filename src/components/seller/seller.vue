<template lang="pug">
  div.seller(ref="seller")
    div.seller-content
      div.overview
        h1.title {{seller.name}}
        div.desc.border-1px-bottom
          star(:size="36", :score="seller.score")
          span.text ({{seller.ratingCount}})
          span.text 月售{{seller.sellCount}}单
        ul.remark
          li.block.border-1px-right
            h2 起送价
            div.content
              span.stress {{seller.minPrice}}
              span 元
          li.block.border-1px-right
            h2 商家配送
            div.content
              span.stress {{seller.deliveryPrice}}
              span 元
          li.block
            h2 平均配送时间
            div.content
              span.stress {{seller.deliveryTime}}
              span 元
        .favorite(@click="toggleFavorite($event)")
          span.icon-favorite(:class="{'active': favorite}")
          span.text {{favoriteText}}
      split
      div.bulletin
        h1.title 公告与活动
        div.content-wrapper.border-1px-bottom
          p.content {{seller.bulletin}}
      ul.supports(v-if="seller.supports")
        li.support-item.border-1px-bottom(v-for="(item,index) in seller.supports", :key="index")
          span.icon(:class="classMap[seller.supports[index].type]")
          span.text {{seller.supports[index].description}}
      split
      div.pics
        h1.title 商家实景
        div.pic-wrapper(ref="picWrapper")
          ul.pic-list(ref="picList")
            li.pic-item(v-for="pic in seller.pics")
              img(:src="pic", width="120", height="90")
      split
      div.info
        h1.title.border-1px-bottom 商家信息
        ul
          li.info-item.border-1px-bottom(v-for="info in seller.infos") {{info}}
</template>


<script>
import BScroll from 'better-scroll'
import star from '@/components/star/star'
import split from '@/components/split/split'
import { saveToLocal, loadFromLocal } from '@/common/js/store'

export default {
  name: 'seller',
  data() {
    return {
      favorite: (() => {
        return loadFromLocal(this.seller.id, 'favorite', false)
      })()
    }
  },
  computed: {
    favoriteText: function() {
      return this.favorite ? '已收藏' : '收藏'
    }
  },
  props: {
    seller: {
      type: Object
    }
  },
  created: function() {
    this.classMap = ['decrease', 'discount', 'special', 'invoice', 'guarantee']
    this.$watch('seller', (val, oldVal) => {
      this.$nextTick(() => {
        this._initScroll()
        this._initPicScroll()
      })
    })
  },
  mounted: function() {
    this.$nextTick(() => {
      this._initScroll()
      this._initPicScroll()
    })
  },
  methods: {
    _initScroll: function() {
      if (!this.scroll) {
        this.scroll = new BScroll(this.$refs.seller, {
          click: true
        })
      } else {
        this.scroll.refresh()
      }
    },
    _initPicScroll: function() {
      if (this.seller.pics) {
        let picWidth = 120
        let margin = 6
        let width = (picWidth + margin) * this.seller.pics.length - margin
        this.$refs.picList.style.width = width + 'px'
        if (!this.picScroll) {
          this.picScroll = new BScroll(this.$refs.picWrapper, {
            scrollX: true,
            eventPassthrough: 'vertical'
          })
        } else {
          this.picScroll.refresh()
        }
      }
    },
    toggleFavorite: function(event) {
      if (!event._constructed) {
        return
      }
      this.favorite = !this.favorite
      saveToLocal(this.seller.id, 'favorite', this.favorite)
    }
  },
  components: {
    star,
    split
  }
}
</script>

<style lang="stylus" type="stylesheet/stylus">
@import "../../common/stylus/mixin.styl"

  .seller
    position absolute
    top 174px
    bottom 0
    left 0
    width 100%
    overflow hidden
    .overview
      position relative
      padding 18px
      .title
        margin-bottom 8px
        line-height 14px
        font-size 14px
        color rgb(7, 17, 27)
      .desc
        padding-bottom 18px
        font-size 0
        border-1px-bottom(rgba(7, 17, 27, 0.1))
        .star
          display inline-block
          vertical-align top
          margin-right 8px
        .text 
          display inline-block
          vertical-align top
          margin-right 12px
          line-height 18px
          font-size 10px
          color rgb(77, 85, 93)
      .remark
        display flex
        padding-top 18px
        .block
          flex 1
          text-align center
          border-1px-right(rgba(7, 17, 27, 0.1))
          &:last-child
            border-none()
          h2
            margin-bottom 4px
            line-height 10px
            font-size 10px
            color rgb(147, 153, 159)
          .content
            line-height 24px
            font-size 10px
            color rgb(7, 17, 27)
            .stress
              font-size 24px
      .favorite
        position absolute
        top 18px
        right 17px 
        width 50px
        text-align center
        .icon-favorite
          display block
          margin-bottom 4px
          color #d4d6d9
          line-height 24px
          font-size 24px
          &.active
            color rgb(240, 20, 20)
        .text
          line-height 10px
          font-size 10px
          color rgb(77, 85, 93)
    .bulletin
      padding 18px 18px 0 18px
      .title
        margin-bottom 8px
        line-height 14px
        font-size 14px
        color rgb(7, 17, 27)
      .content-wrapper
        padding 0 12px 16px 12px
        border-1px-bottom(rgba(7, 17, 27, 0.1))
        .content
          line-height 24px
          font-size 12px
          color rgb(240, 20, 20)
    .supports
      padding 0 18px
      .support-item
        padding 16px 12px
        border-1px-bottom(rgba(7, 17, 27, 0.1))
        &:last-child
          border-none()
      .icon
        display: inline-block
        vertical-align: top
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
        line-height 16px
        font-size 12px
        color rgb(7, 17, 27)
    .pics
      padding 18px
      .title
        margin-bottom 12px
        line-height 14px
        color rgb(7, 17, 27)
        font-size 14px
      .pic-wrapper
        width 100%
        overflow hidden
        white-space nowrap
        .pic-list
          font-size 0
          .pic-item
            display inline-block
            margin-right 6px
            width 120px
            height 90px
            &:last-child
              margin 0
    .info
      padding 18px 18px 0 18px
      .title
        padding-bottom 12px
        line-height 14px
        border-1px-bottom(rgba(7, 17, 27, 0.1))
        color rgb(7, 17, 27)
        font-size 14px
      .info-item
        padding 16px 12px
        line-height 16px
        border-1px-bottom(rgba(7, 17, 27, 0.1))
        font-size 12px
        &:last-child
          border-none()
</style>
