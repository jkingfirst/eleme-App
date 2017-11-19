<template>
<div class="rating_type">
    <div class="rating_select">
      <span class="all" @click="select(2)" :class="{'active':ratingType ===2}">{{typeDesc.all}}<span class="count">{{ratings.length}}</span></span>
      <span class="positive" @click="select(0)" :class="{'active':ratingType ===0}">{{typeDesc.positive}}<span class="count">{{positives.length}}</span></span>
      <span class="negative" @click="select(1)" :class="{'active':ratingType ===1}">{{typeDesc.negative}}<span class="count">{{negatives.length}}</span></span>
    </div>
    <div @click.stop="toggleContent" class="only_content">
      <span class="icon-check_circle" :class="{'active':onlyContent}"></span><span class="text">只看有内容的评价</span>
    </div>
</div>
</template>
<script type="text/ecmascript-6">
   const POSITIVE = 0;
   const NEGATIVE = 1;
   const ALL = 2;
export default{
  props: {
      typeDesc: {
          type: Object,
           default () {
              return {
                 all: '全部',
                 positive: '满意',
                negative: '不满意'
              };
           }
      },
      onlyContent: {
          type: Boolean,
          default: false
      },
      ratingType: {
          type: Number,
          default: ALL
      },
      ratings: {
          type: Array,
          default () {
              return [];
          }
      }
  },
  methods: {
      select (type) {
          this.$emit('ratingType', type);
      },
      toggleContent () {
        this.$emit('onlyContent');
      }
  },
  computed: {
      positives () {
          return this.ratings.filter((rating) => {
              return rating.rateType === POSITIVE;
          });
      },
      negatives () {
        return this.ratings.filter((rating) => {
          return rating.rateType === NEGATIVE;
        });
      }
  }
};
</script>
<style lang="scss" scoped>
  @import "../../common/css/common";
.rating_type{
 font-size:0;
  .rating_select{
     padding:18px 0;
     @include border-1px(rgba(7,17,27,.1));
    span{
      font-size:12px;
      line-height:16px;
      display: inline-block;
      padding:8px 12px;
      margin-right:8px;
      border-radius:1px;
      &.active{
        color:#fff;
      }
      span{
        &.count{
          font-size:8px;
          margin:0;
          padding:0;
          margin-left:2px;
        }
      }

    }
    span:nth-child(3){
      margin-right:0
    }
      .all{
        background-color:rgba(0,160,220,.2);
        &.active{
          background-color:rgb(0,160,220);
        }
      }
      .positive{
        background-color:rgba(0,160,220,.2);
        &.active{
          background-color:rgb(0,160,220);
        }
      }
      .negative{
        background-color:rgba(77,85,93,.2);
        &.active{
          background-color:rgb(77,85,93);
        }
      }


  }
  .only_content{
    color:rgb(147,153,159);
    padding:12px 0;
    border-bottom:1px solid rgba(7,17,27,.1);
    .icon-check_circle{
      font-size:24px;
      line-height:24px;
      &.active{
        color:#00c850;
      }
    }
    .text{
      font-size:12px;
      line-height:24px;
      vertical-align: top;
    }
  }
}
</style>
