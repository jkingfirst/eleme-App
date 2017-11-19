<template>
<div class="cartcontrol">
  <transition name="move">
    <span class="cart_decrease" @click.stop.prevent="decrease()" v-show="food.count>0">
      <transition name="rotate">
        <span class="icon-remove_circle_outline"></span>
      </transition>
    </span>
  </transition>
  <span class="cart_count" v-show="food.count>0">{{food.count}}</span>
  <span class="cart_increase icon-add_circle" @click.stop.prevent="increase($event)"></span>
</div>
</template>
<script type="text/ecmascript-6">
  import Vue from 'vue';
export default {
    props: {
        food: {
            type: Object
        }
    },
  methods: {
        increase (event) {
             if (!this.food.count) {
               Vue.set(this.food, 'count', 1);
             } else {
               this.food.count += 1;
             }
             this.$emit('add', event.target);
        },
        decrease () {
            if (this.food.count) {
                this.food.count -= 1;
            }
        }
  }
};
</script>
<style lang="scss" scoped>
  @import "../../common/css/common";
.cartcontrol{
  font-size:0;
  .cart_decrease,.cart_increase{
    padding:6px;
    display:inline-block;
  }
  .cart_decrease{
    &.move-enter-active,&.move-leave-active{
      opacity:1;
      transition:all .3s linear;
      transform:translate3D(0,0,0);
    }
    &.move-enter,&.move-leave-to{
      opacity:0;
      transform:translate3D(12px,0,0);
      .icon-remove_circle_outline{
        transform:rotate(360deg);
      }
    }
    .icon-remove_circle_outline{
      display: inline-block;
      font-size: 24px;
      line-height:24px;
      color: rgb(0, 160, 220);
      transition:all .3s linear;
      transform:rotate(0deg);
    }
  }
  .cart_increase{
    font-size: 24px;
    line-height:24px;
    color: rgb(0, 160, 220);
  }
  .cart_count{
    display: inline-block;
    text-align: center;
    vertical-align:top;
    font-size: 10px;
    width:12px;
    margin-top:6px;
    padding:6px;
    color: rgb(147, 153, 159);
  }
}
</style>
