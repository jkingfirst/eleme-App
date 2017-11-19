<template>
  <div class="seller" ref="seller">
    <div class="seller_wrapper">
      <div class="overview border-1px" >
        <h1 class="name">{{seller.name}}</h1>
        <div class="view_con">
          <div class="star_wrapper">
            <star :size="36" :score="seller.score"></star>
          </div>
          <span class="rating_count">({{seller.ratingCount}})</span>
          <span class="sell_count">月售{{seller.sellCount}}单</span>
        </div>
        <div class="collection">
          <span @click="toggleFavorite" class="icon-favorite" :class="{'active': favorite}"></span>
          <span class="text">{{collection}}</span>
        </div>
      </div>
      <div class="detail">
        <ul>
          <li class="li_item">
            <h1 class="title">起送价</h1>
            <span class="text"><span class="stress">{{seller.minPrice}}</span>元</span>
          </li>
          <li class="li_item">
            <h1 class="title">商家配送</h1>
            <span class="text"><span class="stress">{{seller.deliveryPrice}}</span>元</span>
          </li>
          <li class="li_item">
            <h1 class="title">平均 配送时间</h1>
            <span class="text"><span class="stress">{{seller.deliveryTime}}</span>分</span>
          </li>
        </ul>
      </div>
      <split></split>
      <div class="bulletin">
        <h1 class="title">公告与活动</h1>
        <div class="bulletin_con">{{seller.bulletin}}</div>
      </div>
      <div class="support">
        <ul>
          <li v-for="(item,key) in seller.supports" class="border-1px">
            <span class="icon_wrapper"><icon :size="16" :type="2" :$index="seller.supports[key].type" :seller="seller" ></icon></span>
            <span class="text" v-text="item.description"></span>
          </li>
        </ul>
      </div>
      <split></split>
      <div class="pic_wrapper" >
        <h1 class="title">商家实景</h1>
        <div ref="picWrapper">
          <ul ref="ul">
            <li class="pic_item" v-for="pic in seller.pics"><img width="120" height="90" :src="pic" ></li>
          </ul>
        </div>
      </div>
      <split></split>
      <div class="seller_info">
        <h1 class="title border-1px" >商家信息</h1>
        <ul>
          <li v-for="info in seller.infos">{{info}}</li>
        </ul>
      </div>
    </div>
  </div>
</template>
<script type="text/ecmascript-6">
  import star from 'components/star/star';
  import split from 'components/split/split';
  import BScroll from 'better-scroll';
  import icon from 'components/icon/icon';
export default{
    props: {
        seller: {
            type: Object
        }
    },
  data () {
        return {
          favorite: false
        };
  },
  computed: {
        collection () {
            return this.favorite ? '已收藏' : '收藏';
        }
  },
    watch: {
          'seller' () {
            this._sellerScroll();
            this._picScroll();
          }
    },
  ready () {
      this._sellerScroll();
      this._picScroll();
    },
  methods: {
        _sellerScroll () {
          this.$nextTick(() => {
            if (!this.sellerScroll) {
              this.sellerScroll = new BScroll(this.$refs.seller, {
                click: true
              });
            } else {
              this.sellerScroll.refresh();
            }
          });
        },
        _picScroll () {
            let len = this.seller.pics.length;
            let mar = 6;
            let width = 120;
            this.$refs.ul.style.width = (width + mar) * len - mar + 'px';
            this.$nextTick(() => {
              if (!this.picScroll) {
                this.picScroll = new BScroll(this.$refs.picWrapper, {
                  scrollX: true
                });
              } else {
                this.picScroll.refresh();
              }
            });
        },
        toggleFavorite () {
          this.favorite = !this.favorite;
        }
  },
  components: {
        star,
        icon,
        split
  }
};
</script>
<style lang="scss" scoped>
  @import "../../common/css/common";
.seller{
  position:absolute;
  top:174px;
  left:0;
  bottom: 0;
  width:100%;
  overflow:hidden;
  .overview{
    padding:18px;
    position: relative;
    @include border-1px(rgba(7,17,27,.1));
    .name{
      margin-bottom:8px;
      font-size:14px;
      line-height:14px;
      color:rgb(7,17,27);
    }
    .view_con{
      font-size:0;
      .star_wrapper{
        display:inline-block;
      }
      .rating_count,.sell_count{
        font-size:10px;
        color:rgb(77,85,93);
        line-height:18px;
      }
      .rating_count{
        margin-left:8px;
      }
      .sell_count{
        margin-left:12px;
      }
    }
    .collection{
      position: absolute;
      width:36px;
      top:18px;
      right:12px;
      text-align: center;
      .icon-favorite{
        display: block;
        font-size:24px;
        line-height:24px;
        color:#7e8c8d;
        margin-bottom: 4px;
        &.active{
          color:rgb(220,20,20);
        }
      }
      .text{
        display: block;
        font-size:10px;
        line-height:10px;
        color:rgb(77,85,93);
      }
    }
  }
  .detail{
    padding:18px 0;
    ul{
      display: flex;
      .li_item{
        flex:1;
        text-align: center;
        .title{
          font-size:10px;
          line-height:10px;
          color:rgb(147,153,159);
          margin-bottom:4px;
        }
        .text{
          font-size:9px;
          .stress{
            font-size:24px;
            line-height:24px;
            color:rgb(7,17,27)
          }
        }
      }
    }
  }
  .bulletin{
    padding:18px 12px 16px 12px;
    .title{
      font-size:14px;
      line-height:14px;
      color:rgb(7,17,27);
      margin-bottom:8px;
    }
    .bulletin_con{
      padding:0 12px;
      font-size:12px;
      line-height:24px;
      font-weight:200;
      color:rgb(220,20,20);
    }
  }
  .support{
    padding:16px 18px;
    ul{
      li{
        padding:16px 12px;
        @include border-1px(rgba(7,17,27,.1));
        font-size:0;
        .icon_wrapper{
          vertical-align: top;
          display: inline-block;
        }
        .text{
          display: inline-block;
          margin-left:6px;
          font-size:12px;
          line-height:16px;
          font-weight:200;
          color:rgb(7,17,27);
        }
      }
    }
  }
  .pic_wrapper{
    padding:18px;
    font-size:0;
    overflow: hidden;
    white-space: nowrap;
    .title{
      margin-bottom:12px;
      font-size:14px;
      line-height:24px;
      color:rgb(7,17,27);
    }
    .pic_item{
      display: inline-block;
      margin-right:6px;
      &.last-child{
        margin-right:0;
      }
    }
  }
  .seller_info{
    padding:18px;
    .title{
      font-size:14px;
      line-height:14px;
      color:rgb(7,17,27);
      @include border-1px(rgba(7,17,27,.1));
      padding:12px 0;
    }
    ul{
      li{
       padding:16px 12px;
        @include border-1px(rgba(7,17,27,.1));
        font-size:12px;
        font-weight:200;
        line-height:16px;
        color:rgb(7,17,27);

      }
    }
  }
}
</style>
