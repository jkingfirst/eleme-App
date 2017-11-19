<template>
  <div class="ratings" ref="ratingScroll">
    <div class="ratings_wrapper">
        <div class="overview">
          <div class="view_left">
            <h1 class="score">{{seller.score}}</h1>
            <div class="view_rating">综合评分</div>
            <div class="detail">高于周边商家{{seller.rankRate}}%</div>
          </div>
          <div class="view_right">
            <div class="service_score">
              <span class="title">服务态度</span>
              <div class="star_wrapper"><star :size="36" :score="seller.serviceScore"></star></div>
              <span class="score">{{seller.serviceScore}}</span>
            </div>
            <div class="food_score">
              <span class="title">食物评分</span>
              <div class="star_wrapper"> <star :size="36" :score="seller.foodScore"></star></div>
              <span class="score">{{seller.foodScore}}</span>
            </div>
            <div class="delivery">
              <span class="delivery_title">送达时间</span>
              <span class="delivery_time">{{seller.deliveryTime}}分钟</span>
            </div>
          </div>
        </div>
        <split></split>
        <div class="rating_wrapper">
          <ratingtype @ratingType="ratingSelect " @onlyContent="toggleContent" :only-content="onlyContent" :rating-type="ratingType" :ratings="ratings"></ratingtype>
        </div>
        <div class="rating_content">
          <ul>
            <li v-show="selectShow(rating.rateType,rating.text)" class="rating_item border-1px" v-for="rating in ratings">
              <div class="avatar">
                <img width="28" height="28" :src="rating.avatar" >
              </div>
              <div class="item_con">
                <span class="name">{{rating.username}}</span>
                <div class="star_wrapper">
                  <star :size="24" :score="rating.score"></star>
                </div>
                <span class="delivery_time">{{rating.deliveryTime}}分钟送达</span>
                <div class="rating_text">{{rating.text}}</div>
                <div class="recommend" v-show="rating.recommend&&rating.recommend.length">
                  <span class="icon-thumb_up"></span>
                  <span class="recommend_item" v-for="item in rating.recommend">{{item}}</span>
                </div>
                <div class="feedback_time">{{rating.rateTime | formatDate}}</div>
              </div>
            </li>
          </ul>
        </div>
    </div>
</div>
</template>
<script type="text/ecmascript-6">
  import BScroll from 'better-scroll';
  import {formatDate} from '../../common/js/formatDate';
  import star from 'components/star/star';
  import split from 'components/split/split';
  import ratingtype from 'components/ratingtype/ratingtype';
  const ERR_OK = 0;
  const ALL = 2;
export default{
  props: {
      seller: Object
  },
  data () {
    return {
     ratings: [],
      onlyContent: true,
      ratingType: ALL
    };
  },
  created () {
      this.$http.get('/api/ratings').then((response) => {
          let result = response.body;
          if (result.errno === ERR_OK) {
             this.ratings = result.data;
             this.$nextTick(() => {
                   if (!this.ratingScroll) {
                     this.ratingScroll = new BScroll(this.$refs.ratingScroll, {
                       click: true
                     });
                   } else {
                       this.ratingScroll.refresh();
                   }
             });
          }
      });
  },
  methods: {
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
          this.ratingScroll.refresh();
        });
      },
      toggleContent () {
        this.onlyContent = !this.onlyContent;
        this.$nextTick(() => {
          this.ratingScroll.refresh();
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
      star,
      split,
    ratingtype
  }
};
</script>
<style lang="scss" scoped>
  @import '../../common/css/common';
.ratings{
  position:absolute;
  top:174px;
  bottom:0;
  left:0;
  width:100%;
  overflow: hidden;
  .overview{
    padding:18px 0;
    display: flex;
    .view_left{
      text-align: center;
      padding:6px 0;
      flex:0 0 137px;
      width:137px;
      border-right:1px solid rgba(7,17,27,.1);
      .score{
        font-size:24px;
        line-height:28px;
        color:rgb(255,153,0);
        padding-bottom:6px;
      }
      .view_rating{
        font-size:12px;
        line-height:12px;
        color:rgb(7,17,27);
        padding-bottom:8px;
      }
      .detail{
        font-size:10px;
        line-height:10px;
        color:rgb(7,17,27);
      }
    }
    .view_right{
      flex: 1;
      padding-left:18px;
      font-size:0;
      @media screen and (max-width:320px){
        padding-left:4px;
      }
      .service_score,.food_score{
        margin-bottom:8px;
        .title{
          font-size:12px;
          color:rgb(7,17,27);
          line-height:18px;
        }
        .star_wrapper{
          margin-left:12px;
          display: inline-block;
          vertical-align: top;
          @media screen and (max-width:320px){
            margin-left:4px;
          }
        }
        .score{
          font-size:12px;
          line-height:18px;
          color:rgb(255,153,0);
          margin-left:6px;
        }
      }
      .delivery{
        .delivery_title{
          font-size:12px;
          line-height:18px;
          color:rgb(7,17,27);
        }
        .delivery_time{
          font-size:12px;
          line-height:18px;
          color:rgb(147,153,159);
          margin-left:12px;
        }
      }
    }

  }
  .rating_wrapper{
    padding:0 18px;
  }
  .rating_content{
    padding:18px;
    ul{
      .rating_item{
        display: flex;
        padding-bottom:18px;
        @include border-1px(rgba(7,17,27,.1));
        .avatar{
          flex:0 0 28px;
          img{
            border-radius:50%;
          }
        }
        .item_con{
          position: relative;
          flex: 1;
          margin-left:12px;
          font-size:0;
          .name{
            display: block;
            font-size:10px;
            color:rgb(7,17,27);
            line-height:12px;
            margin-bottom:4px;
          }
          .star_wrapper{
            display: inline-block;
            margin-bottom: 6px;
          }
          .delivery_time{
            display: inline-block;
            font-size:10px;
            line-height:12px;
            color:rgb(147,153,159);
            font-weight:200;
            margin:0 0 6px 6px;
          }
          .rating_text{
            display: block;
            margin-bottom:8px;
            font-size:12px;
            line-height:18px;
            color:rgb(7,17,27);
          }
          .recommend{
            .icon-thumb_up{
              color:rgb(0,160,220);
              font-size:12px;
              line-height:16px;
              margin-right:8px;
            }
            .recommend_item{
              font-size:9px;
              padding:0 6px;
              line-height:16px;
              color:rgb(147,153,159);
              border:1px solid rgba(7,17,27,.1);
              border-radius:2px;
              background-color:#fff;
              margin-right:6px;
            }
          }
          .feedback_time{
            position: absolute;
            top:0;
            right:0;
            font-size:10px;
            line-height:12px;
            font-weight:200;
            color:rgb(147,153,159);
          }
        }
      }
    }
  }
}
</style>
