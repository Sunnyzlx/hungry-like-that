<template lang="pug">
  div.split.border-1px-top.border-1px-bottom
</template>

<script>
export default {
  name: 'split'
}
</script>

<style lang="stylus">
@import "../../common/stylus/mixin.styl"
  .split
    width 100%
    height 16px
    background #f3f5f7
    border-1px-top(rgba(7, 17, 27, 0.1))
    border-1px-bottom(rgba(7, 17, 27, 0.1))
</style>
