<template lang="pug">
  div.ratingselect
    div.rating-type.border-1px-bottom
      span.block.positive(:class="{'active': selectedType===2}", @click="select(2, $event)") {{desc.all}} 
        span.count {{rating.length}}
      span.block.positive(:class="{'active': selectedType===0}", @click="select(0, $event)") {{desc.positive}}
        span.count {{positives.length}}
      span.block.negative(:class="{'active': selectedType===1}", @click="select(1, $event)") {{desc.negative}}
        span.count {{negatives.length}}
    div.switch(:class="{'on': onlyCon}", @click="toggleContent($event)")
      span.icon-check_circle
      span.text 只看有内容的评价
</template>

<script>
const POSITIVE = 0
const NEGATIVE = 1
const ALL = 2

export default {
  name: 'ratingselect',
  data() {
    return {
      selectedType: this.selectType,
      onlyCon: this.onlyContent
    }
  },
  props: {
    rating: {
      type: Array,
      default() {
        return []
      }
    },
    selectType: {
      type: Number,
      default: ALL
    },
    onlyContent: {
      type: Boolean,
      default: false
    },
    desc: {
      type: Object,
      default() {
        return {
          all: '全部',
          positive: '满意',
          negative: '不满意'
        }
      }
    }
  },
  computed: {
    positives() {
      return this.rating.filter((rating) => {
        return rating.rateType === POSITIVE
      })
    },
    negatives() {
      return this.rating.filter((rating) => {
        return rating.rateType === NEGATIVE
      })
    }
  },
  methods: {
    select(type, event) {
      if (!event._constructed) {
        return
      }
      this.selectedType = type
    },
    toggleContent(event) {
      if (!event._constructed) {
        return
      }
      this.onlyCon = !this.onlyCon
    }
  }
}
</script>

<style lang="stylus">
@import "../../common/stylus/mixin.styl"
  .ratingselect
    .rating-type
      padding 18px 0
      margin 0 18px
      border-1px-bottom(rgba(7, 17, 27, 0.1))
      font-size 0
      .block
        display inline-block
        padding 8px 12px
        margin-right 8px
        border-radius 1px
        font-size 12px
        color rgb(77, 85, 93)
        &.active
          color #fff
        .count
          font-size 8px
          margin-left 2px
          line-height 16px
        &.positive
          background rgba(0, 160, 220, 0.2)
          &.active
            background rgb(0, 160, 220)
        &.negative
          background rgba(77, 85, 93, 0.2)
          &.active
            background rgb(77, 85, 93)
    .switch
      padding 12px 18px
      line-height 24px
      border-1px-bottom(rgba(7, 17, 27, 0.1))
      color rgb(147, 153, 159)
      font-size 0
      &.on
        .icon-check_circle
          color #00c850
      .icon-check_circle
        display inline-block
        vertical-align top
        margin-right 4px
        font-size 24px
      .text
        display inline-block
        vertical-align top
        font-size 12px
</style>

