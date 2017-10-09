<template>
  <div class="title">
    <div class="line"></div>
    <div class="text">{{titleText}}</div>
    <div class="line"></div>
  </div>
</template>

<script>
export default {
  name: 'title',
  props: {
    titleText: {
      type: String
    }
  }
}
</script>

<style lang="stylus" rel="stylesheet/stylus">
  .title
    display: flex
    width: 100%
    .line
      flex: 1
      position: relative
      top: -9px
      border-bottom: 1px solid rgba(255, 255, 255, 0.5)
    .text
      font-size: 14px
      padding: 0 12px
      font-weight: 700
</style>
