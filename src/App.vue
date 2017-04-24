<template>
  <div id="app">
    <v-header :seller="seller"></v-header>
    <div class="tab">
      <div class="tab-item">
        <router-link to="/goods">商品</router-link>
      </div>
      <div class="tab-item">
        <router-link to="/ratings">评论</router-link>
      </div>
      <div class="tab-item">
        <router-link to="/seller">商家</router-link>
      </div>
    </div>
    <keep-alive>
      <router-view :seller="seller"></router-view>
    </keep-alive>
  </div>
</template>

<script>
import header from './components/header/header'
import {urlParse} from './common/js/util'

const ERR_OK = 0

export default {
  data () {
    return {
      seller: {
        id: (() => {
          let queryParam = urlParse()
          // console.log(queryParam)
          return queryParam.id
        })()
      }
    }
  },
  created () {
    this.$http.get('/api/seller?id=' + this.seller.id).then(response => {
      response = response.body
      if (response.errno === ERR_OK) {
        // 获取数据from 'data.json'
        // this.seller = response.data
        // 为seller.id扩展属性
        this.seller = Object.assign({}, this.seller, response.data)
      }
    })
  },
  components: {
    'v-header': header
  }
}
</script>

<style lang="scss">
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}
.tab {
  display: flex;
  width: 100%;
  height: 0.8rem;
  line-height: 0.8rem;
  border-bottom: 0.01rem solid rgba(7, 17, 27, 0.1);
  padding-bottom: 0.02rem;

  .tab-item {
    flex: 1;
    font-size: 0.28rem;
    color: rgb(77,85,93);

    .active{
      display: block;
      color: rgb(240, 20, 20);
      border-bottom: 0.02rem solid rgb(240, 20, 20);
      padding-bottom: 0;
    }
  }
}
</style>
