<template>
  <div class="goods">
    <div class="menu-wapper" ref="menuWapper">
      <ul>
        <li v-for="(item,index) in goods" class="menu-item" :class="{'current' : currentIndex === index}" @click="selectMenu(index, $event)">
          <span class="text">
            <span v-show="item.type > 0" :class="classMap[item.type]" class="icon"></span>{{ item.name }}
          </span>
        </li>
      </ul>
    </div>
    <div class="foods-wrapper" ref="foodsWapper">
      <ul>
        <li v-for="item in goods" class="food-list food-list-hook">
          <h1 class="title"> {{ item.name }} </h1>
          <ul>
            <li v-for="food in item.foods" class="food-item" @click="selectFood(food,$event)">
              <div class="icon">
                <img :src="food.icon" alt="">
              </div>
              <div class="content">
                <h2 class="name">{{ food.name }}</h2>
                <p class="desc">{{ food.description }}</p>
                <div class="extra">
                  <span>月售{{ food.sellCount }}份</span>
                  <span>好评率{{ food.rating }}%</span>
                </div>
                <div class="price">
                  <span class="now">
                    ￥{{ food.price }}
                  </span>
                  <span v-show="food.oldPrice" class="oldprice">
                    ￥{{ food.oldPrice }}
                  </span>
                </div>
                <div class="cartcontrol-wrapper">
                  <cartcontrol :food="food"></cartcontrol>
                </div>
              </div>
            </li>
          </ul>
        </li>
      </ul>
    </div>
    <shopcart :select-foods="selectFoods" :delivery-price="seller.deliveryPrice" :min-price="seller.minPrice"></shopcart>
    <food :food="selectedFood" ref="food"></food>
  </div>
</template>

<script>
import BScroll from 'better-scroll'
import shopcart from '../shopcart/shopcart'
import cartcontrol from '../cartcontrol/cartcontrol'
import food from '../food/food'

const ERR_OK = 0

export default {
  props: {
    // 接收APP.vue 'router-view'组件传过来的对象
    seller: {
      type: Object
    }
  },
  data () {
    return {
      goods: [],
      listHight: [],  // 右侧分类高度数组
      scrollY: 0,
      selectedFood: {}
    }
  },
  computed: {
    // 计算当前索引值(添加高亮效果)
    currentIndex () {
      for (let i = 0; i < this.listHight.length; i++) {
        let heightUp = this.listHight[i]
        let heightDown = this.listHight[i + 1]
        if (!heightDown || this.scrollY >= heightUp && this.scrollY < heightDown) {
          return i
        }
      }
      return 0
    },
    // 选择食品
    selectFoods () {
      let foods = []
      this.goods.forEach((good) => {
        good.foods.forEach((food) => {
          if (food.count) {
            foods.push(food)
          }
        })
      })
      return foods
    }
  },
  created () {
    this.classMap = ['decrease', 'discount', 'special', 'invoice', 'guarantee']
    this.$http.get('/api/goods').then(response => {
      response = response.body
      if (response.errno === ERR_OK) {
        this.goods = response.data
        // $nextTick用于DOM渲染完成后
        this.$nextTick(() => {
          this._initScroll()
          this._calculateHight()
        })
      }
    })
  },
  methods: {
    // 初始化better-scroll插件
    _initScroll: function () {
      this.menuScroll = new BScroll(this.$refs.menuWapper, {
        click: true
      })
      this.foodsScroll = new BScroll(this.$refs.foodsWapper, {
        click: true,
        probeType: 3
      })
      // 监听右侧滚动位置
      this.foodsScroll.on('scroll', (pos) => {
        this.scrollY = Math.abs(Math.round(pos.y))
      })
    },
    // 返回右侧分类高度数组
    _calculateHight: function () {
      let foodList = this.$refs.foodsWapper.getElementsByClassName('food-list-hook')
      let height = 0
      this.listHight.push(height)
      for (let i = 0; i < foodList.length; i++) {
        let item = foodList[i]
        height += item.clientHeight
        this.listHight.push(height)
      }
    },
    // 点击左侧菜单分类实现右侧分类滚动到顶部（左右联动）
    selectMenu: function (index, event) {
      if (!event._constructed) {
        return
      }
      let foodList = this.$refs.foodsWapper.getElementsByClassName('food-list-hook')
      let el = foodList[index]
      this.foodsScroll.scrollToElement(el, 300)
    },
    selectFood: function (food, event) {
      if (!event._constructed) {
        return
      }
      this.selectedFood = food
      this.$refs.food.show()
    }
  },
  components: {
    shopcart,
    cartcontrol,
    food
  }
}
</script>

<style lang="scss">
.goods {
  display: flex;
  position: absolute;
  top: 3.5rem;
  bottom: 0.96rem;
  width: 100%; 
  overflow: hidden;
  .menu-wapper {
    flex: 0 0 1.6rem;
    width: 1.6rem;
    background-color: #f3f5f7;
    .menu-item {
      display: table;
      padding: 0 0.24rem;
      width: 1.6rem;
      height: 1.08rem;
      font-size: 0.28rem;
      line-height: 0.28rem;
      box-sizing: border-box;
      .text {
        display: table-cell;
        vertical-align: middle;
        margin-left: 0.04rem;
        width: 1.12rem;
        border-bottom: 1px solid rgba(7, 17, 27, 0.1);
        .icon {
          display: inline-block;
          width: 0.24rem;
          height: 0.24rem;
        }
      }
    }
    .current {
      position: relative;
      z-index: 10;
      margin-top: -0.01rem;
      font-weight: 700;
      background-color: #fff;
      .text {
        border-bottom: 0;
      }
    }
  }
  .foods-wrapper {
    flex: 1;
    text-align: left;
    .title {
      padding-left: 0.28rem;
      height: 0.52rem;
      color: rgb(147, 153, 159);
      font-size: 0.24rem;
      line-height: 0.52rem;
      border-left: 0.04rem solid #d9dde1;
      background-color: #f3f5f7;
    }
    .food-item {
      display: flex;
      margin: 0.36rem;
      border-bottom: 1px solid rgba(7, 17, 27, 0.1);
      .content {
        flex: 1;
        position: relative;
        margin-left: 0.2rem;
        .name {
          margin-top: 0.04rem;
          font-size: 0.28rem;
          line-height: 0.28rem;
          color: rgb(7, 17, 27);
        }
        .desc {
          margin-top: 0.16rem;
          font-size: 0.2rem;
          line-height: 0.2rem;
          color: rgb(147, 153, 159);
        }
        .extra {
          margin-top: 0.16rem;
          font-size: 0.2rem;
          line-height: 0.2rem;
          color: rgb(147, 153, 159);
        }
        .price {
          color: red;
          font-size: 0.28rem;
          line-height: 0.48rem;
          font-weight: 700;
          .oldprice {
            font-size: 0.24rem;
            text-decoration: line-through;
            color: rgb(147, 153, 159);
          }
        }
        .cartcontrol-wrapper {
          position: absolute;
          right: 0;
          bottom: 0;
        }
      }
    }
    .food-item:last-child {
      border-bottom: 0;
    }
  }
}
.decrease {
  background: url(decrease_3@2x.png) no-repeat;
}
.discount {
  background: url(discount_3@2x.png) no-repeat;
}
.special {
  background: url(special_3@2x.png) no-repeat;
}
.invoice {
  background: url(invoice_3@2x.png) no-repeat;
}
.guarantee {
  background: url(guarantee_3@2x.png) no-repeat;
}
</style>
