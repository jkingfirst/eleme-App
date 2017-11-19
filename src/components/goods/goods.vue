<template>
  <div>
    <div class="goods">
      <div class="menu_wrapper" ref="menuWrapper">
        <ul>
          <li class="menu_item" v-for="(item,key) in goods" :class="{current:current===key}" @click="selectItem(key)">
          <span class="title border-1px">
              <span v-if="item.type>0" class="icon_wrapper">
              <icon :size="12" :type="2" :$index="item.type" :goods="goods"></icon>
              </span>
              <span class="text">{{item.name}}</span>
          </span>
          </li>
        </ul>
      </div>
      <div class="goods_wrapper"  ref="goodsWrapper">
        <ul>
          <li class="food_item food_item_hook" v-for="item in goods">
            <h1 class="title" v-text="item.name"></h1>
            <ul>
              <li @click="currentFood(food)" class="food_list" v-for="food in item.foods">
                <div class="thumbnails">
                  <img :src="food.icon" :alt="food.name">
                </div>
                <div class="content">
                  <h2 v-text="food.name" class="name"></h2>
                  <p v-text="food.description" class="desc"></p>
                  <p><span class="sell_count">月售{{food.sellCount}}份</span><span class="rating">好评率{{food.rating}}%</span></p>
                  <p><span class="now_price">¥{{food.price}}</span><span class="old_price" v-if="food.oldPrice!==''">¥{{food.oldPrice}}</span></p>
                  <div class="cartcontrol_wrapper"><cartcontrol :food="food" @add="addFood"></cartcontrol></div>
                </div>
              </li>
            </ul>
          </li>
        </ul>
      </div>
    </div>
    <shopcart ref="shopcart" :select-food="selectFood" :seller="seller"></shopcart>
    <div class="food_wrapper" >
      <food :food="currentFoods"ref="good"></food>
    </div>
  </div>
</template>
<script type="text/ecmascript-6">
  import icon from 'components/icon/icon';
  import BetterScroll from 'better-scroll';
  import shopcart from 'components/shopcart/shopcart';
  import cartcontrol from 'components/cartcontrol/cartcontrol';
  import food from 'components/food/food';
  const ERR_OK = 0;
  export default{
      props: {
          seller: {
              type: Object
          }
      },
      data () {
          return {
              goods: [],
              listHeight: [],
              scrollY: 0,
              currentFoods: {}
          };
      },
      created () {
         this.$http.get('/api/goods').then((res) => {
           let response = res.body;
           if (response.errno === ERR_OK) {
             this.goods = response.data;
             this.$nextTick(() => {
               this._initBscroll();
               this._calculate();
             });
           }
         });
      },
    components: {
          icon,
          shopcart,
          cartcontrol,
          food
    },
    computed: {
       current () {
           for (let i = 0; i < this.listHeight.length; i++) {
               let height1 = this.listHeight[i];
               let height2 = this.listHeight[i + 1];
               if (!height2 || (this.scrollY >= height1 && this.scrollY < height2)) {
                   return i;
               };
           }
           return 0;
       },
      selectFood () {
           let foods = [];
           this.goods.forEach((good) => {
               good.foods.forEach((food) => {
                   if (food.count) {
                       foods.push(food);
                   }
               });
           });
           return foods;
      }
    },
    methods: {
          selectItem (index) {
              let foodItem = this.$refs.goodsWrapper.getElementsByClassName('food_item_hook');
              let el = foodItem[index];
              this.goodsScroll.scrollToElement(el, 300);
          },
          _initBscroll () {
              this.menuScroll = new BetterScroll(this.$refs.menuWrapper, {
                  click: true
              });
              this.goodsScroll = new BetterScroll(this.$refs.goodsWrapper, {
                probeType: 3,
                click: true
              });
              this.goodsScroll.on('scroll', (pos) => {
                this.scrollY = Math.abs(Math.round(pos.y));
              });
          },
          _calculate () {
                  let listHeight = this.$refs.goodsWrapper.getElementsByClassName('food_item_hook');
                  let len = listHeight.length;
                  let height = 0;
                  this.listHeight.push(height);
                  for (let i = 0; i < len; i++) {
                      let item = listHeight[i];
                      height += item.clientHeight;
                      this.listHeight.push(height);
                  }
          },
          addFood (target) {
              this._drop(target);
          },
          _drop (target) {
            // 体验优化,异步执行下落动画
            this.$nextTick(() => {
              this.$refs.shopcart.drop(target);
            });
          },
          currentFood (food) {
              this.currentFoods = food;
              this.$refs.good.show();
          }
    }
  };
</script>
<style lang="scss" scoped>
  @import "../../common/css/common";
.goods{
  position:absolute;
  top:174px;
  bottom:46px;
  width: 100%;
  display: flex;
  overflow: hidden;
  .menu_wrapper{
    flex: 0 0 80px;
    width:80px;
    background:#f3f5f7;
    ul{
      li{
        &.menu_item{
          height:54px;
          width:56px;
          display: table;
          padding:0 12px;
          vertical-align: middle;
          line-height:14px;
          &.current{
            background-color:#fff;
            position: relative;
            z-index: 10;
            top:1px;
            span{
              &.title:after{
                border-bottom:none;
                .text{
                  font-size:12px;
                  color:#000;
                  font-weight:700;
                  line-height:14px;
                }
              }

            }
          }
          span{
            &.title{
              display: table-cell;
              vertical-align: middle;
              @include border-1px(rgba(7,17,27,.1));
              .icon_wrapper{
              }
              .text{
                font-size:12px;
                color:rgb(24,20,20);
              }
            }
          }
        }
      }
    }
  }
  .goods_wrapper{
    flex:1;
    ul{
      li{
        &.food_item{
          h1{
            &.title{
              font-size:12px;
              color:rgb(147,153,159);
              line-height:26px;
              padding-left:14px;
              border-left:1px solid #d9dde1;
              background:#f3f5f7;
            }
          }
          ul{
            li{
              &.food_list{
                display: flex;
                margin:18px;
                padding-bottom:18px;
                @include border-1px(rgba(7,17,27,.1));
                .thumbnails{
                  flex:0 0 57px;
                  width:57px;
                  margin-right:10px;
                  img{
                    width:100%;
                  }
                }
                .content{
                  flex:1;
                  h2{
                    &.name{
                      font-size:14px;
                      line-height:14px;
                      color:rgb(7,17,27);
                      margin:2px auto 8px;
                    }
                  }
                  p{
                    &.desc{
                      margin:8px 0;
                      font-size:10px;
                      color:rgb(147,158,159);
                    }
                  }
                  p{
                    span{
                      font-size:10px;
                      color:rgb(147,158,159);
                      &.sell_count{
                        margin-right:12px;
                      }
                    }
                  }
                  p{
                    span{
                      &.now_price{
                        margin-right:8px;
                        font-size:14px;
                        color:#f00;
                        font-weight:700;
                        line-height:24px;
                      }
                      &.old_price{
                        font-size:10px;
                        color :rgb(147,153,159);
                        font-weight:700;
                        line-height:10px;
                      }
                    }
                  }
                  .cartcontrol_wrapper{
                    position:absolute;
                    right:0;
                    bottom:12px;
                  }
                }
              }
            }
          }
        }
      }
    }
  }
}
</style>
