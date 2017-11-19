<template>
  <div id="app">
    <v-header :seller="seller"></v-header>
    <nav class="nav border-1px">
      <ul>
        <li><router-link :to="{path:'/goods'}">商品</router-link></li>
        <li><router-link :to="{path:'/ratings'}">评价</router-link></li>
        <li><router-link :to="{path:'/seller'}">商家</router-link></li>
      </ul>
    </nav>
    <keep-alive>
       <router-view :seller="seller" ></router-view>
    </keep-alive>
  </div>
</template>

<script type="text/ecmascript-6">

import header from './components/header/header.vue';
const ERR_OK = 0;
export default {
  name: 'app',
  data () {
    return {
      seller: {}
    };
  },
  created () {
    this.$http.get('/api/seller').then(res => {
      let response = res.body;
      if (response.errno === ERR_OK) {
        this.seller = response.data;
      }
    });
  },
  components: {
    'v-header': header
  }
};
</script>

<style lang="scss" scoped>
  @import "./common/css/common.scss";
  #app {
    font-family: 'Avenir', Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
  }
  .nav{
    height:40px;
    width:100%;
    @include border-1px(rgba(7,17,27,.2));
    ul{
      display: flex;
      display: -webkit-flex;
      width:100%;
      height:100%;
      li{
        flex: 1;
        line-height:40px;
        text-align: center;
        a{
          display:block;
          color:rgb(77,85,93);
          font-size:14px;
          &.active{
            color:rgb(240,20,20);
          }
        }
      }
    }
  }
</style>
