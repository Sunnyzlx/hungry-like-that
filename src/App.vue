<template>
  <div id="app">
    <v-header :seller='seller'></v-header>
    <div class="tab border-1px">
      <router-link to="/" tag="div" class="tab-item">商品</router-link>
      <router-link to="/ratings" tag="div" class="tab-item">评论</router-link>
      <router-link to="/seller" tag="div" class="tab-item">商家</router-link>
    </div>
    <router-view></router-view>
  </div>
</template>

<script>
import header from '@/components/header/header'
const ERR_OK = 0

export default {
  name: 'app',
  data() {
    return {
      seller: {}
    }
  },
  created() {
    this.$http.get('/api/seller').then(res => {
      res = res.body
      if (res.errno === ERR_OK) {
        this.seller = res.data
      }
    })
  },
  components: {
    vHeader: header
  }
}
</script>

<style lang="stylus" type="stylesheet/stylus">
@import './common/stylus/mixin.styl'

#app
  .tab
    display: flex
    width: 100%
    height: 40px
    line-height: 40px
    border-1px-top(rgba(7, 17, 27, 0.1))
    border-1px-bottom(rgba(7, 17, 27, 0.1))
    .tab-item
      flex: 1
      font-size: 14px
      text-align: center
      color: rgb(77, 85, 93)
    .router-link-exact-active
        color: rgb(240, 20, 20)
</style>
