<template>
  <transition name="move">
    <div v-show="showFlag" class="food" ref="foodScroll">
  <div class="food_header">
    <div class="food_img">
      <img :src="food.image" :alt="food.name">
    </div>
    <div @click="hide()" class="back">
      <span class="icon-arrow_lift"></span>
    </div>
    <div class="food_content">
      <h1 class="name">{{food.name}}</h1>
      <div class="detail">
        <span class="sell_count">月售{{food.sellCount}}份</span><span class="ratings">好评率{{food.rating}}%</span>
      </div>
      <div class="price">
        <span class="now">¥{{food.price}}</span>
        <span class="old" v-show="food.oldPrice">¥{{food.oldPrice}}</span>
      </div>
      <div class="cartcontrol_wrapper">
        <cartcontrol @add="addFood" :food="food"></cartcontrol>
      </div>
      <transition name="fade">
       <div v-show="!food.count" @click="addCart" class="addCart">加入购物车</div>
      </transition>
    </div>
    <split v-show="food.info"></split>
    <div class="food_info" v-show="food.info">
      <h1 class="title">商品介绍</h1>
      <div class="text">{{food.info}}</div>
    </div>
    <split></split>
    <div class="food_rating">
      <h1 class="title">商品评价</h1>
      <ratingtype @ratingType="ratingSelect" @onlyContent="toggleContent" :type-desc="typeDesc" :only-content="onlyContent" :rating-type="ratingType" :ratings="food.ratings"></ratingtype>
      <div class="rating_con">
        <ul>
          <li class="rating_item border-1px" v-for="rating in food.ratings" v-show="selectShow(rating.rateType,rating.text)" >
            <div class="rating_time">{{rating.rateTime | formatDate}}</div>
            <div class="rating_text">
              <span :class="{'icon-thumb_up':rating.rateType===0,'icon-thumb_down':rating.rateType===1}"></span>
              <span class="text">{{rating.text}}</span>
            </div>
            <div class="rating_user">
              <span class="name">{{rating.username}}</span>
              <img class="avatar" :src="rating.avatar" width="12" height="12" >
            </div>
          </li>
        </ul>
      </div>
      <div class="no_rating" v-show="!food.ratings||!food.ratings.length">暂无评论</div>
    </div>
  </div>
  this.food
</div>
  </transition>
</template>
<script type="text/ecmascript-6">
  import BScroll from 'better-scroll';
  import cartcontrol from 'components/cartcontrol/cartcontrol';
  import split from 'components/split/split';
  import {formatDate} from '../../common/js/formatDate';
  import ratingtype from 'components/ratingtype/ratingtype';
  import Vue from 'vue';
  // const POSITIVE = 0;
  // const NEGATIVE = 1;
  const ALL = 2;
export default{
    props: {
        food: {
            type: Object
        }
    },
  data () {
        return {
          showFlag: false,
          typeDesc: {
              all: '全部',
              positive: '推荐',
              negative: '吐槽'
          },
          onlyContent: false,
          ratingType: ALL,
          ratings: []
        };
  },
  methods: {
        show () {
            this.showFlag = true;
            this.onlyContent = true;
            this.ratingType = ALL;
                this.$nextTick(() => {
                    if (!this.foodScroll) {
                      this.foodScroll = new BScroll(this.$refs.foodScroll, {
                        click: true
                      });
                    } else {
                        this.foodScroll.refresh();
                    }
                });
        },
        hide () {
            this.showFlag = false;
        },
        addCart () {
            Vue.set(this.food, 'count', 1);
            this.$emit('add', event.target);
        },
        addFood (target) {
          this.$emit('add', target);
        },
        selectShow (type, text) {
          if (this.onlyContent && !text) {
              return false;
          }
          if (this.ratingType === ALL) {
              return true;
          } else {
             return this.ratingType === type;
          }
        },
    ratingSelect (type) {
      this.ratingType = type;
      this.$nextTick(() => {
        this.foodScroll.refresh();
      });
    },
    toggleContent () {
      this.onlyContent = !this.onlyContent;
      this.$nextTick(() => {
        this.foodScroll.refresh();
      });
    }
  },
  filters: {
        formatDate (time) {
            let date = new Date(time);
            return formatDate(date, 'YYYY-MM-DD hh:mm');
        }
  },
  components: {
        cartcontrol,
        split,
        ratingtype
  }
};
</script>
<style lang="scss" scoped>
  @import '../../common/css/common';
.food{
  position:fixed;
  top:0;
  left:0;
  bottom:48px;
  width:100%;
  height:100%;
  background-color:#fff;
  z-index:9;
  &.move-enter-active,&move-leave-active{
    transition:all .3s linear;
    transform:translate3d(0,0,0);
  }
  &.move-leave-active{
    transition:all .3s linear;
  }
  &.move-enter,&.move-leave-to{
    transform:translate3d(100%,0,0);
  }
  .food_header{
    .food_img{
      position: relative;
      left:0;
      top: 0;
      width:100%;
      height:0;
      padding-top:100%;
      img{
        position:absolute;
        left:0;
        top:0;
        width:100%;
        height:100%;
      }
    }
    .back{
      position: absolute;
      left:0;
      top:10px;
      .icon-arrow_lift{
        display: block;
        padding:10px;
        color:#fff;
        font-size:25px;
      }
    }
    .food_content{
      position: relative;
      padding:18px;
      .name{
        font-size:14px;
        font-weight:700;
        color:rgb(7,17,27);
        line-height:14px;
      }
      .detail{
        margin-top:8px;
        .sell_count,.ratings{
          display: inline-block;
          font-size:10px;
          color:rgb(147,153,159);
          line-height:10px;
        }
        .sell_count{
          margin-right:12px;
        }
      }
      .price{
        margin-top:18px;
        .now{
          font-size:14px;
          font-weight:700;
          color:rgb(240,20,20);
          line-height:24px;
        }
        .old{
          text-decoration: line-through;
          font-size:10px;
          color:rgb(147,153,159);
          font-weight:700;
          line-height:24px;
          margin-left:12px;
        }
      }
      .cartcontrol_wrapper{
        position:absolute;
        bottom:6px;
        right:12px;
      }
      .addCart{
        position:absolute;
        z-index:10;
        bottom:12px;
        right:18px;
        width:74px;
        height:24px;
        box-sizing:border-box;
        text-align:center;
        line-height:24px;
        font-size:10px;
        border-radius:12px;
        color:#fff;
        background-color:rgb(0,160,220);
        &.fade-enter-active,&.fade-leave-active{
          opacity: 1;
          transition:all .3s;
        }
        &.fade-enter,&.fade-leave-to{
          opacity: 0;
        }

      }
    }
    .food_info{
      padding:18px;
      .title{
        line-height: 14px;
        margin-bottom: 6px;
        font-size: 14px;
        color: #07111b;
      }
      .text{
        padding:8px;
        font-size:12px;
        font-weight:200;
        line-height:20px;
        color:rgb(77,85,93);
      }
    }
    .food_rating{
      padding:18px;
      .title{
        line-height: 14px;
        font-size: 14px;
        color: #07111b;
      }
      .rating_con{
        .rating_item{
          padding:16px 0;
          position:relative;
          @include border-1px(rgba(7,17,27,.1));
          .rating_time{
            color:rgb(147,153,159);
            font-size:10px;
            line-height:12px;
          }
          .rating_text{
            font-size:0;
            .icon-thumb_up,.icon-thumb_down{
              font-size:12px;
              line-height:24px;
              color:rgb(147,153,159);
            }
            .icon-thumb_up{
              color:rgb(0,160,220);
            }
            .text{
              font-size:12px;
              color:rgb(147,153,158);
              line-height:12px;
              margin-left:4px;
            }
          }
          .rating_user{
            position:absolute;
            right:0;
            top:16px;
            font-size:0;
            .name{
              color:rgb(147,153,159);
              display: inline-block;
              line-height:12px;
              font-size:10px;
              margin-right:6px;
            }
            .avatar{
              display: inline-block;
              border-radius:50%
            }
          }
        }
      }
      .no_rating{
        padding:16px 0;
        font-size:12px;
        line-height:12px;
        color:rgb(143,157,159);
      }
    }
  }


}
</style>
