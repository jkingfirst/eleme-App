<template>
  <span class="icon" :class="sizeType">
    <span :class="classMap[$index][type-1]"></span>
  </span>

</template>
<script type="text/ecmascript-6">
  export default {
    props: {
         size: {
             type: Number
         },
          $index: {
            type: Number
          },
          seller: {
            type: Object
          },
          goods: {
             type: Array
          },
         type: {
               type: Number
         }
     },
    computed: {
      sizeType () {
        return 'icon_' + this.size;
      }
    },
    created () {
         this.classMap = [
           ['decrease_1', 'decrease_2'],
           ['discount_1', 'discount_2'],
           ['special_1', 'special_2'],
           ['invoice_1', 'invoice_2'],
           ['guarantee_1', 'guarantee_2']
         ];
    }
  };
</script>
<style lang="scss" scoped>
  @import '../../common/css/common';
  .icon{
    display:inline-block;
    &.icon_12{
      span{
        width:12px;
        height:12px;
        display: block;
        &.decrease_1{
          @include bg-img('decrease_1')
        }
        &.decrease_2{
          @include bg-img('decrease_3')
        }
        &.discount_1{
          @include bg-img('discount_1')
        }
        &.discount_2{
          @include bg-img('discount_3')
        }
        &.special_1{
          @include bg-img('special_1')
        }
        &.special_2{
          @include bg-img('special_3')
        }
        &.invoice_1{
          @include bg-img('invoice_1')
        }
        &.invoice_2{
          @include bg-img('invoice_3')
        }
        &.guarantee_1{
          @include bg-img('guarantee_1')
        }
        &.guarantee_2{
          @include bg-img('guarantee_3')
        }
      }
    }
    &.icon_16{
      span{
        width:16px;
        height:16px;
        display:block;
        &.decrease_1{
          @include bg-img('decrease_2')
        }
        &.decrease_2{
          @include bg-img('decrease_4')
        }
        &.discount_1{
          @include bg-img('discount_2')
        }
        &.discount_2{
          @include bg-img('discount_4')
        }
        &.special_1{
          @include bg-img('special_2')
        }
        &.special_2{
          @include bg-img('special_4')
        }
        &.invoice_1{
          @include bg-img('invoice_2')
        }
        &.invoice_2{
          @include bg-img('invoice_4')
        }
        &.guarantee_1{
          @include bg-img('guarantee_2')
        }
        &.guarantee_2{
          @include bg-img('guarantee_4')
        }
      }
    }

  }
</style>
