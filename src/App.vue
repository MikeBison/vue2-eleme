<template>
  <div class="app">
    <div class="header">
      <v-header :seller="seller"></v-header>
    </div>
    <div class="tab border-1px">
      <div class="tab-item">
        <router-link to="/goods">
          商品
        </router-link>
      </div>
      <div class="tab-item">
        <router-link to="/ratings">
          评论
        </router-link>
      </div>
      <div class="tab-item">
        <router-link to="/seller">
          商家
        </router-link>
      </div>
    </div>
    <router-view :seller="seller"></router-view>
  </div>
</template>

<script type="text/ecmascript-6">
  import header from './components/header/header.vue';
  const ERR_OK = 0;
  export default{
    data () {
        return {
          seller: {}
        };
    },
    created () {
      this.$http.get('/api/seller').then((res) => {
        res = res.body;
        if (res.errno === ERR_OK) {
          this.seller = res.data;
        }
      });
    },
    components: {
        'v-header': header
    }
  };
</script>

<style lang="stylus" rel="styltsheet/stylus">
  @import './common/stylus/mixin.styl'
  .tab
    display: flex
    width: 100%
    /*border-bottom: 1px solid rgba(7,1,27,0.1)*/
    border-1px(rgba(7,17,27,0.1))
    height: 40px
    line-height: 40px
    .tab-item
      flex: 1
      text-align: center
      &>a
        display: block
        font-size: 14px;
        color: rgb(77,85,93)
        &.active
          color: rgb(240,20,20)
</style>
