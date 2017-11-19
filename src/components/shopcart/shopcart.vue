<template>
<div>
  <div class="shopcart">
    <div class="content" @click="showList">
      <div class="content_left">
        <div class="log_wrapper">
          <div class="log" :class="{'highlighted':count>0}">
            <span class="icon-shopping_cart" :class="{'highlighted':count>0}"></span>
          </div>
          <div class="num" v-if="count>0">{{count}}</div>
        </div>
        <div class="total" :class="{'highlighted':total>0}">¥{{total}}</div>
        <div class="delivery">另需配送费{{seller.deliveryPrice}}元</div>
      </div>
      <div class="content_right" @click.stop.prevent="pay()">
        <div class="pay" :class="payClass">{{payType}}</div>
      </div>
    </div>
    <div class="ball_wrapper">
      <div v-for="ball in balls">
      <transition name="drop" @before-enter="beforeEnter" @enter="enter" @after-enter="afterEnter">
         <div class="ball" v-show="ball.show">
           <div class="inner inner-hook"></div>
         </div>
      </transition>
      </div>
    </div>
    <transition name="fold">
      <div class="cart_list_wrapper" v-show="listShow">
        <div class="list_header">
          <h1 class="title">购物车</h1>
          <span class="empty" @click="empty">清空</span>
        </div>
        <div class="list_content" ref="listContent">
          <ul>
            <li class="selectFood" v-for="food in selectFood">
              <span class="name">{{food.name}}</span>
              <span class="total">¥{{food.price*food.count}}</span>
              <div class="cartcontrol_wrapper">
                <cartcontrol @add="addFood" :food="food"></cartcontrol>
              </div>
            </li>
          </ul>
        </div>
      </div>
    </transition>
  </div>
  <transition name="fade">
     <div class="list_mask" v-show="listShow"  @click="hideList"></div>
  </transition>
</div>
</template>
<script type="text/ecmascript-6" >
  import cartcontrol from 'components/cartcontrol/cartcontrol';
  import BScroll from 'better-scroll';
  export default{
      props: {
        seller: {
            type: Object
        },
        selectFood: {
            type: Array,
          default () {
                return [
                  {
                    price: 10,
                    count: 1
                  }
                ];
          }
        }
      },
      data () {
         return {
           balls: [
             {show: false},
             {show: false},
             {show: false},
             {show: false},
             {show: false}
           ],
           dropballs: [],
           fold: true
         };
      },
    computed: {
          total () {
              let total = 0;
              this.selectFood.forEach((food) => {
                  total += food.price * food.count;
              });
              return total;
          },
          count () {
              let count = 0;
              this.selectFood.forEach((food) => {
                  count += food.count;
              });
              return count;
          },
          payType () {
             if (this.count === 0) {
                 return `¥${this.seller.minPrice}起送`; // 反单引号es6语法可以拼接字符串
             } else if (this.total < this.seller.minPrice) {
                 let diff = this.seller.minPrice - this.total;
                 return `还差¥${diff}元起送`;
             } else {
                 return '去结算';
             }
          },
          payClass () {
              if (this.total < this.seller.minPrice) {
                  return 'not_enough';
              } else {
                  return 'enough';
              }
          },
          listShow () {
            if (!this.count) {
                this.fold = true;
                return false;
            }
            let show = !this.fold;
            if (show) {
                this.$nextTick(() => {
                  if (!this.LScroll) {
                    this.LScroll = new BScroll(this.$refs.listContent, {
                      click: true
                    });
                  } else {
                      this.LScroll.refresh();
                  }
                });
            }
            return show;
          }
    },
    methods: {
          drop (el) {
            for (let i = 0; i < this.balls.length; i++) {
              let ball = this.balls[i];
              if (!ball.show) {
               ball.show = true;
               ball.el = el;
               this.dropballs.push(ball);
               return;
              }
            }
          },
          showList () {
              if (!this.count) {
                  return;
              }
              this.fold = !this.fold;
          },
          empty () {
              this.selectFood.forEach((food) => {
                  food.count = 0;
              });
          },
          hideList () {
              this.fold = true;
          },
          pay () {
              window.alert(`应支付¥${this.total}`);
          },
          addFood (target) {
           this.drop(target);
          },
          beforeEnter (el) {
            let count = this.balls.length;
            while (count--) {
              let ball = this.balls[count];
              if (ball.show) {
                let rect = ball.el.getBoundingClientRect();
                let x = rect.left - 32;
                let y = -(window.innerHeight - rect.top - 22);
                el.style.display = '';
                el.style.webkitTransform = `translate3d(0,${y}px,0)`;
                el.style.transform = `translate3d(0,${y}px,0)`;
                let inner = el.getElementsByClassName('inner-hook')[0];
                inner.style.webkitTransform = `translate3d(${x}px,0,0)`;
                inner.style.transform = `translate3d(${x}px,0,0)`;
              }
            }
          },
          enter (el, done) {
            /* eslint-disable no-unused-vars */
            let rf = el.offsetHeight;
            this.$nextTick(() => {
              el.style.webkitTransform = 'translate3d(0,0,0)';
              el.style.transform = 'translate3d(0,0,0)';
              let inner = el.getElementsByClassName('inner-hook')[0];
              inner.style.webkitTransform = 'translate3d(0,0,0)';
              inner.style.transform = 'translate3d(0,0,0)';
              el.addEventListener('transitionend', done);
            });
          },
          afterEnter (el) {
            let ball = this.dropballs.shift();
            if (ball) {
              ball.show = false;
              el.style.display = 'none';
            }
          }
    },
    components: {
          cartcontrol
    }
  };
</script>
<style lang="scss" scoped>
  @import '../../common/css/common.scss';
.shopcart{
  width:100%;
  height:48px;
  z-index: 20;
  position: fixed;
  bottom:0;
  left:0;
  .content{
    background-color:#141d27;
    display:flex;
    font-size:0;
    .content_left{
      flex:1;
      .log_wrapper{
        display: inline-block;
        position:relative;
        box-sizing:border-box;
        top:-10px;
        margin:0 18px;
        padding:6px;
        width:56px;
        height:56px;
        border-radius:50%;
        background-color:#141d27;
        .log{
          width:100%;
          height:100%;
          border-radius:50%;
          background-color:#2b343c;
          text-align:center;
          &.highlighted{
            background: rgb(0, 160, 220);
          }
          .icon-shopping_cart{
            font-size:24px;
            color:rgba(255,255,255,.4);
            line-height:44px;
            &.highlighted{
              color:#fff;
            }
          }
        }
        .num{
          position:absolute;
          color:#fff;
          top:0;
          right:0;
          width:24px;
          height:16px;
          font-size:9px;
          background-color:rgb(240, 20, 20);
          box-shadow:0 4px 8px 0 rgba(0,0,0,0.4);
          border-radius:16px;
          text-align:center;
          line-height:16px;
        }
      }
      .total{
        display: inline-block;
        font-size:16px;
        color:rgba(255,255,255,.4);
        font-weight:700;
        line-height:24px;
        border-right:1px solid rgba(255,255,255,.1);
        vertical-align: top;
        margin:12px 0;
        padding-right:12px;
        box-sizing:border-box;
        &.highlighted{
          color:#fff;
        }
      }
      .delivery{
        display: inline-block;
        vertical-align: top;
        margin: 12px 0 0 12px;
        line-height: 24px;
        font-size: 10px;
        color:rgba(255,255,255,.4)
      }
    }
    .content_right{
      flex:0 0 105px;
      width:105px;
      .pay{
        height:56px;
        line-height:56px;
        text-align:center;
        color: rgba(255, 255, 255, 0.4);
        background: #2b333b;
        font-size: 12px;
        font-weight: 700;
        &.enough{
          background-color: #00b43c;
          color:#fff;
        }
        &.not_enough{
          background: #2b333b;
        }
      }
    }
  }
  .ball_wrapper{
    .ball{
      position:fixed;
      left:32px;
      bottom:22px;
      z-index:200;
      transition: all 0.4s cubic-bezier(0.49, -0.29, 0.75, 0.41);
        .inner{
          width:16px;
          height:16px;
          border-radius:50%;
          transition:all .4s linear;
          background-color:rgb(0,160,220);
        }

    }
  }
  .cart_list_wrapper{
    position:absolute;
    left:0;
    top:0;
    width:100%;
    z-index:-1;
    background: #fff;
    transform:translate3d(0,-100%,0);
    &.fold-enter-active,&.fold-leave-active{
      transition:all .5s;
    }
    &.fold-enter,&.fold-leave-to{
      transform:translate3d(0,0,0);
    }
    .list_header{
      height:40px;
      padding:0 18px;
      line-height:40px;
      background-color:#f3f5f7;
      border-bottom:1px solid rgba(7,17,27,.1);
      .title{
        float:left;
        font-size:14px;
        color:rgb(7,17,27);
      }
      .empty{
        float:right;
        color:rgb(0,160,220);
        font-size:12px;
      }
    }
    .list_content{
      max-height:218px;
      overflow: hidden;
      .selectFood{
        height:48px;
        box-sizing:border-box;
        display: flex;
        line-height:24px;
        padding:12px 18px;
        @include border-1px(rgba(7,17,27,.1));
        .name{
          flex: 1;
        }
        .total{
          flex:0 0 30px;
          width:48px;
          font-size:10px;
          color:rgb(240,20,20);
          font-weight:700;
        }
        .cartcontrol_wrapper{
          flex:0 0 96px;
          width:96px;
          line-height:14px;
          position: relative;
          top:-6px;
        }
      }
    }
  }
}
.list_mask{
  position:fixed;
  left:0;
  top:0;
  width: 100%;
  height:100%;
  z-index:10;
  background: rgba(7,17,27,.6);
  -webkit-backdrop-filter: blur(10px);
  &.fade-enter-active,&.fade-leave-active{
    transition:all .5s;
    opacity:1;
  }
  &.fade-enter,&.fade-leave-to{
    opacity:0;
  }
}

</style>
