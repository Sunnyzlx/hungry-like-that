<template lang="pug">
  div.ratings(ref="ratings")
    div.rating-content
      div.overview
        div.overview-left.border-1px-right
          div.score {{seller.score}}
          div.title 综合评分
          div.rank 高于周边商家{{seller.rankRate}}%
        div.overview-right
          div.score-wrapper
            span.title 服务态度
            star(:size="36", :score="seller.serviceScore")
            span.score {{seller.serviceScore}}
          div.score-wrapper
            span.title 商品评分
            star(:size="36", :score="seller.foodScore")
            span.score {{seller.foodScore}}
          div.delivery-wrapper
            span.title 送达时间
            span.delivery {{seller.deliveryTime}}分钟
      split
      ratingselect(@selected="selectedType", @toggle="toggleCon", :rating="ratings", :select-type="selectType", :only-content="onlyContent")
      div.rating-wrapper
        ul
          li.rating-item.border-1px-bottom(v-show="needShow(rating.rateType, rating.text)",v-for="rating in ratings")
            div.avatar
              img(:src="rating.avatar", width="28", height="28")
            div.content
              h1.name {{rating.username}}
              div.star-wrapper
                star(:size="24", :score="rating.score")
                span.deivery(v-show="rating.deliveryTime") {{rating.deliveryTime}}分钟送达
              p.text {{rating.text}}
              div.recommend(v-show="rating.recommend && rating.recommend.length")
                span.icon-thumb_up
                span.item(v-for="item in rating.recommend") {{item}}
              div.time {{rating.rateTime | formateDate}}
</template>


<script>
import BScroll from 'better-scroll'
import star from '@/components/star/star'
import split from '@/components/split/split'
import ratingselect from '@/components/ratingselect/ratingselect'
import { formateDate } from '@/common/js/date'
const ERR_OK = 0
const ALL = 2

export default {
  name: 'ratings',
  data() {
    return {
      ratings: [],
      selectType: ALL,
      onlyContent: true
    }
  },
  props: {
    seller: {
      type: Object
    }
  },
  created() {
    this.$http.get('api/ratings').then((response) => {
      response = response.body
      if (response.errno === ERR_OK) {
        this.ratings = response.data
        this.$nextTick(() => {
          this.scroll = new BScroll(this.$refs.ratings, {
            click: true
          })
        })
      }
    })
  },
  methods: {
    needShow(type, text) {
      if (this.onlyContent && !text) {
        return false
      }
      if (this.selectType === ALL) {
        return true
      } else {
        return type === this.selectType
      }
    },
    selectedType(type) {
      this.selectType = type
      this.$nextTick(() => {
        this.scroll.refresh()
      })
    },
    toggleCon(type) {
      this.onlyContent = type
      this.$nextTick(() => {
        this.scroll.refresh()
      })
    }
  },
  filters: {
    formateDate: function(time) {
      let date = new Date(time)
      return formateDate(date, 'yyyy-MM-dd hh:mm')
    }
  },
  components: {
    star,
    split,
    ratingselect
  }
}
</script>

<style lang="stylus" type="stylesheet/stylus">
@import "../../common/stylus/mixin.styl"

  .ratings 
    position absolute
    top 174px
    bottom 0
    left 0
    width 100%
    overflow hidden
    .overview
      display flex
      padding 18px 0
      .overview-left
        flex 0 0 137px
        padding 6px 0
        width 137px
        border-1px-right(rgba(7, 17, 27, 0.1))
        text-align center
        @media only screen and (max-width: 320px)
          flex 0 0 120px
          width 120px
        .score
          margin-bottom 6px
          line-height 28px
          font-size 24px
          color rgb(255, 153, 0)
        .title
          margin-bottom 8px
          line-height 12px
          font-size 12px
          color rgb(7, 17, 27)
        .rank
          line-height 10px
          font-size 10px
          color rgb(147, 153, 159)
      .overview-right
        flex 1
        padding 6px 0 6px 24px
        @media only screen and (max-width: 320px)
          padding-left 6px
        .score-wrapper
          margin-bottom 8px
          line-height 18px
          font-size 0
          .title
            display inline-block
            vertical-align top
            font-size 12px
            color rgb(7, 17, 27)
          .star
            display inline-block
            vertical-align top
            margin 0 12px
          .score
            display inline-block
            vertical-align top
            font-size 12px
            color rgb(255, 153, 0)
        .delivery-wrapper
          font-size 0
          .title
            line-height 18px
            font-size 12px
            color rgb(7,17, 27)
          .delivery
            margin-left 12px
            font-size 12px
            color rgb(147, 153, 159)
    .rating-wrapper
      padding 0 18px
      .rating-item
        display flex
        padding 18px 0
        border-1px-bottom(rgba(7, 17, 27, 0.1))
        .avatar
          flex 0 0 28px
          width 28px
          margin-right 12px
          img
            border-radius 50%
        .content
          position relative
          flex 1
          .name
            margin-bottom 4px
            line-height 12px
            font-size 10px
            color rgb(7, 17, 27)
          .star-wrapper
            margin-bottom 6px
            font-size 0
            .star
              display inline-block
              vertical-align top
              margin-right 6px
            .delivery
              display inline-block
              vertical-align top
              line-height 12px
              font-size 10px
              color rgb(147, 153, 159)
          .text
            margin-bottom 8px
            line-height 18px
            color rgb(7, 17, 27)
            font-size 12px
          .recommend
            line-height 16px
            font-size 0
            .icon-thumb_up, .item
              display inline-block
              margin 0 8px 4px 0
              font-size 9px
            .icon-thumb_up
              color rgb(0, 160, 220)
            .item
              padding 0 6px
              border 1px solid rgba(7, 17, 27, 0.1)
              border-radius 1px
          .time
            position absolute
            top 0
            right 0
            line-height 12px
            font-size 10px
            color rgb(147, 153, 159)
</style>
