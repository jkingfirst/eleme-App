<template>
   <div class="star" :class="sizeType">
     <span v-for="starType in itemClasses" track-by="$index" :class="starType" class="star_item"></span>
   </div>
</template>
<script type="text/ecmascript-6">
  const CLS_HALF = 'half';
  const CLS_OFF = 'off';
  const CLS_ON = 'on';
  const LEN = 5;
  export default {
      props: {
          size: {
              type: Number
          },
          score: {
              type: Number
          }
      },
    computed: {
      sizeType () {
        return 'star_' + this.size;
      },
      itemClasses () {
          let result = [ ];
          let score = Math.floor(this.score * 2) / 2;
          let hasDecimal = score % 2 !== 0;
          let integer = Math.floor(this.score);
          for (let i = 0; i < integer; i++) {
              result.push(CLS_ON);
          }
          if (hasDecimal) {
              result.push(CLS_HALF);
          }
          while (result.length < LEN) {
              result.push(CLS_OFF);
          }
          return result;
      }
    }
  };
</script>
<style lang="scss" scoped>
  @import "../../common/css/common";
.star{
  font-size:0;
  .star_item{
    display: inline-block;
  }
  &.star_48{
    .star_item{
      width:20px;
      height:19px;
      margin-right:22px;
      &.on{
        @include bg-img('star48_on');
      }
      &.half{
        @include bg-img('star48_half');
      }
      &.off{
        @include bg-img('star48_off');
      }
      &:last-child{
        margin-right:0;
      }
    }
  }
  &.star_36{
    .star_item{
      width:15px;
      height:14px;
      margin-right:6px;
      &.on{
        @include bg-img('star36_on');;
      }
      &.half{
        @include bg-img('star36_half');
      }
      &.off{
        @include bg-img('star36_off');
      }
      &:last-child{
        margin-right:0;
      }
    }
  }
  &.star_24{
    .star_item{
      width:10px;
      height:10px;
      margin-right:3px;
      &.on{
        @include bg-img('star24_on');
      }
      &.half{
        @include bg-img('star24_half');
      }
      &.off{
        @include bg-img('star24_off');
      }
      &:last-child{
        margin-right:0;
      }
    }
  }
}
</style>
