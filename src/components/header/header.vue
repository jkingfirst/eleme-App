<template>
  <div class="header">
    <div class="content_wrapper">
      <div class="avatar">
        <img width="64" height="64" :src="seller.avatar" alt="">
      </div>
      <div class="content">
        <div class="title">
          <span class="brand"></span>
          <h2 class="name" v-text="seller.name"></h2>
        </div>
        <div class="description">
          <span v-text="seller.description"></span>/<span v-text="seller.deliveryTime"></span>分钟送达
        </div>
        <div class="support" v-if="seller.supports">
          <span class="icon_wrapper"><icon :size="12" :type="1" :$index="seller.supports[0].type" :seller="seller" ></icon></span>
          <span v-text="seller.supports[0].description"></span>
        </div>
      </div>
      <div v-on:click="showDetail()" v-if="seller.supports" class="support_count">
        <span class="count" >{{seller.supports.length}}个</span>
        <i class="icon-keyboard_arrow_right"></i>
      </div>
    </div>
    <div @click="showDetail()" class="bulletin_wrapper">
      <span class="bulletin_title"></span><span class="bulletin_text" v-text="seller.bulletin"></span>
      <span class="icon-keyboard_arrow_right"></span>
    </div>
    <div class="bg">
      <img :src="seller.avatar" width="100%" height="100%">
    </div>
    <transition name="fade">
      <div v-show="detailShow" class="detail" >
      <div class="detail_wrapper">
        <div class="detail_main ">
            <h1 v-text="seller.name"></h1>
            <div class="star_wrapper">
              <star :size="48" :score="seller.score"></star>
            </div>
            <div class="title">
              <div class="line"></div>
              <div class="text">优惠信息</div>
              <div class="line"></div>
            </div>
            <div v-if="seller.supports" class="supports">
              <ul>
                <li v-for="(item,key) in seller.supports">
                  <span class="icon_wrapper"><icon :size="16" :type="1" :$index="seller.supports[key].type" :seller="seller" ></icon></span>
                  <span class="text" v-text="item.description">{{key}}</span>
                </li>
              </ul>
            </div>
          <div class="title">
            <div class="line"></div>
            <div class="text">商家公告</div>
            <div class="line"></div>
          </div>
          <div class="bulletin">
            <p class="bulletin_content" v-text="seller.bulletin"></p>
          </div>
        </div>
      </div>
      <div @click="closeDetail" class="detail_close">
        <span class="icon-close"></span>
      </div>
    </div>
    </transition>
  </div>
</template>
<script type="text/ecmascript-6">
  import star from '../star/star';
  import icon from '../icon/icon';
  export default {
      props: {
          seller: {
             type: Object
        }
      },
    data () {
       return {
           detailShow: false
       };
    },
    methods: {
          showDetail () {
              this.detailShow = true;
          },
          closeDetail () {
              this.detailShow = false;
          }
    },
    created () {
         this.classMap = ['decrease', 'discount', 'special', 'invoice', 'guarantee'];
    },
    components: {
          star,
          icon
    }
  };
</script>
<style lang="scss" scoped>
  @import '../../common/css/common';
.header{
  position: relative;
  color:#fff;
  overflow: hidden;
  background: rgba(7,17,27,.5);
  .content_wrapper{
      padding:24px 12px 18px 24px;
      position: relative;
      font-size:0;
      .avatar,.content{
        display: inline-block;
      }
      .avatar{
        img{
          border-radius:2px;
        }
      }
      .content{
        margin-left:16px;
        vertical-align: top;
        .title{
          padding:2px 0 8px 0;
          font-size:0;
          .brand{
            display: inline-block;
            vertical-align: top;
            width:30px;
            height:18px;
            @include bg-img('brand');
            background-repeat: no-repeat;
            background-size:100%;
          }
          h2{
            display: inline-block;
            &.name{
              font-size:16px;
              line-height:18px;
              font-weight:bold;
              margin-left:6px;
            }
          }
        }
        .description{
          font-size:12px;
          line-height:12px;
          margin-bottom:5px;
        }
        .support{
          font-size:10px;
          line-height:10px;
          .icon_wrapper{
            vertical-align:top;
            display:inline-block;
          }
        }
      }
      .support_count{
        position:absolute;
        font-size:10px;
        line-height:10px;
        right:12px;
        bottom:12px;
        height:24px;
        text-align: center;
        line-height:24px;
        padding:0 8px;
        border-radius:14px;
        background:rgba(0,0,0,.2);
        .count{
          vertical-align: top;
        }
        i{
          &.icon-keyboard_arrow_right{
            font-size:10px;
            line-height:24px;
            margin-left:2px;
          }
        }
      }


  }
  .bulletin_wrapper{
    height:28px;
    line-height:28px;
    position: relative;
    overflow: hidden;
    white-space:nowrap;
    text-overflow:ellipsis;
    padding:0 22px 0 12px;
    background:rgba(7,17,27,.2);
    .bulletin_title{
       display: inline-block;
       width:22px;
       height:12px;
       @include bg-img('bulletin');
    }
    .bulletin_text{
      vertical-align: top;
      font-size:10px;
       margin: 0 4px;
    }
    .icon-keyboard_arrow_right{
      position: absolute;
      right:12px;
      bottom:6px;
      font-size:10px;
    }
  }
  .bg{
    z-index: -1;
    position: absolute;
    left:0;
    top:0;
    height:100%;
    width:100%;
    filter:blur(10px)
  }
  .detail{
    position:fixed;
    z-index: 100;
    top:0;
    left:0;
    width:100%;
    height:100%;
    overflow:auto;
    overflow-scrolling:touch;
    background: rgba(7,17,27,.8);
    &.fade-enter-active,&.fade-leave-active{
      transition:all .5s linear;
      opacity: 1;
      background: rgba(7,17,27,.8);
    }
    &.fade-enter,&.fade-leave-to{
       opacity: 0;
       background: rgba(7,17,27,0);
    }
    .detail_wrapper{
        min-height:100%;
        height:auto !important;
        height:100%;
        &:after{
          content:'';
          display:table;
          clear:both;
        }
        .detail_main{
           padding-top:64px;
           padding-bottom:64px;
          h1{
            display: block;
            text-align: center;
            font-weight:700;
            font-size:16px;
            line-height:16px;
          }
          .star_wrapper{
            text-align: center;
            margin:16px auto 28px;
          }
          .title{
            display: flex;
            width:80%;
            margin:28px auto 24px;
            .line{
              flex:1;
              position:relative;
              top:-6px;
              border-bottom:1px solid rgba(255,255,255,.2);
            }
            .text{
              font-size:16px;
              font-weight:700;
              line-height:16px;
              padding:0 6px;
            }
          }
          .supports{
            width:80%;
            margin:0 auto;
            ul{
              width:100%;
              li{
                margin-bottom: 12px;
                &:last-child{
                  margin-bottom:0
                }
                span{
                  &.icon_wrapper{
                    display: inline-block;
                  }
                }
                span{
                  &.text{
                    vertical-align: top;
                    margin-left:6px;
                    font-size:12px;
                    line-height:12px;
                  }
                }
              }
            }
          }
          .bulletin{
            width:80%;
            margin:0 auto;
            p{
              &.bulletin_content{
                padding:0 12px;
                line-height:24px;
                font-size:12px;
              }
            }
          }
        }
    }
    .detail_close{
      position:relative;
      clear:both;
      color:rgba(255,255,255,.5);
      width:32px;
      height:32px;
      font-size:32px;
      z-index: 101;
      margin:-64px auto 0;
    }
  }

}
</style>
