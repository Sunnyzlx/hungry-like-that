<template lang="pug">
  transition(name="move")
    div.food(v-show="showFlag")
      div.food-content
        div.image-header
          img(:src="food.image")
          div.back(@click="hide")
            i.icon-arrow_lift
        div.content
          h1.title {{food.name}}
          div.detail
            span.sell-count 月售{{food.sellcount}}份
            span.rating 好评率{{food.rating}}%
          div.price
            span.now ￥{{food.price}}
            span.old(v-show="food.oldPrice") ￥{{food.oldPrice}}
</template>

<script>
export default {
  name: 'food',
  data() {
    return {
      showFlag: false
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
    },
    hide: function() {
      this.showFlag = false
    }
  }
}
</script>


<style lang="stylus">
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
</style>
