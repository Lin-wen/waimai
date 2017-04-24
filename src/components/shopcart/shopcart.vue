<template>
  <div>
    <div class="shopcart">
      <div class="content" @click="toggleList">
        <div class="content-left">
          <div class="logo-wrapper">
            <div class="logo" :class="{'highlight' : totalCount > 0}">
              <i class="icon-shopping_cart" :class="{'highlight' : totalCount > 0}"></i>
            </div>
            <div class="num" v-show="totalCount > 0">{{ totalCount }}</div>
          </div>
          <div class="price" :class="{'highlight' : totalCount > 0}">￥{{ totalPrice }}</div>
          <div class="desc">另需配送费{{ deliveryPrice }}元</div>
        </div>
        <div class="content-right" :class="payClass">
          <div class="pay">{{ payDesc }}</div>
        </div>
      </div>
      <transition name="fold">
        <div class="shopcart-list" v-show="listshow">
          <div class="list-header">
            <h1 class="title">购物车</h1>
            <span class="empty" @click="empty">清空</span>
          </div>
          <div class="list-content" ref="listContent">
            <ul>
              <li class="food" v-for="food in selectFoods">
                <span class="name">{{ food.name }}</span>
                <div class="price">
                  <span>￥{{ food.price*food.count }}</span>
                </div>
                <div class="cartcontrol-wrapper">
                  <cartcontrol :food="food"></cartcontrol>
                </div>
              </li>
            </ul>
          </div>
        </div>
      </transition>
    </div>
    <transition name="fade">
      <div class="list-mask" v-show="listshow"></div>
    </transition>
  </div>
</template>

<script>
import BScroll from 'better-scroll'
import cartcontrol from '../cartcontrol/cartcontrol'

export default {
  props: {
    selectFoods: {
      type: Array,
      default () {
        return []
      }
    },
    deliveryPrice: {
      type: Number,
      default: 0
    },
    minPrice: {
      type: Number,
      default: 0
    }
  },
  data () {
    return {
      fold: true
    }
  },
  computed: {
    totalPrice () {
      let total = 0
      this.selectFoods.forEach((food) => {
        total += food.price * food.count
      })
      return total
    },
    totalCount () {
      let count = 0
      this.selectFoods.forEach((food) => {
        count += food.count
      })
      return count
    },
    payDesc () {
      if (this.totalPrice === 0) {
        return `￥${this.minPrice}元起送`
      } else if (this.totalPrice < this.minPrice) {
        let diff = this.minPrice - this.totalPrice
        return `还差￥${diff}元起送`
      } else {
        return `去结算`
      }
    },
    payClass () {
      if (this.totalPrice >= this.minPrice) {
        return 'highlight'
      }
    },
    listshow () {
      if (!this.totalCount) {
        this.fold = true
        return false
      }
      let show = !this.fold
      if (show) {
        this.$nextTick(() => {
          if (!this.scoll) {
            this.scoll = new BScroll(this.$refs.listContent, {
              click: true
            })
          } else {
            this.scoll.refresh()
          }
        })
      }
      return show
    }
  },
  methods: {
    toggleList () {
      if (!this.totalCount) {
        return
      }
      this.fold = !this.fold
    },
    empty () {
      this.selectFoods.forEach((food) => {
        food.count = 0
      })
    }
  },
  components: {
    cartcontrol
  }
}
</script>

<style lang="scss">
.shopcart {
  position: fixed;
  left: 0;
  bottom: 0;
  width: 100%;
  height: 0.96rem;
  z-index: 99;
  .content {
    display: flex;
    font-size: 0;
    height: 0.96rem;
    .content-left {
      flex: 1;
      height: 0.96rem;
      text-align: left;
      background-color: #141d27;
      .logo-wrapper {
        display: inline-block;
        position: relative;
        top: -0.2rem;
        margin: 0 0.24rem;
        padding: 0.12rem;
        width: 1.12rem;
        height: 1.12rem;
        box-sizing: border-box;
        vertical-align: top;
        border-radius: 50%;
        background-color: #141d27;
        .logo {
          width: 100%;
          height: 100%;
          text-align: center;
          border-radius: 50%;
          background-color: #2b343c;
          .icon-shopping_cart {
            font-size: 0.48rem;
            line-height: 0.88rem;
            color: #80858a;
          }
          .highlight {
            color: #fff;
          }
        }
        .logo.highlight {
          background-color: #00a0dc;
        }
        .num {
          position: absolute;
          top: 0;
          right: 0;
          width: 0.48rem;
          height: 0.32rem;
          line-height: 0.32rem;
          border-radius: 0.32rem;
          font-size: 0.18rem;
          font-weight: 700;
          text-align: center;
          color: #fff;
          background-color: rgb(240, 20, 20);
          box-shadow: 0 0.08rem 0.16rem 0 rgba(0, 0, 0, 0.4)
        }
      }
      .price {
        display: inline-block;
        vertical-align: top;
        margin-top: 0.24rem;
        line-height: 0.48rem;
        padding-right: 0.24rem;
        box-sizing: border-box;
        color: rgba(255, 255, 255, 0.4);
        font-size: 0.32rem;
        font-weight: 700;
        border-right: 0.01rem solid rgba(255, 255, 255, 0.1);
      }
      .price.highlight{
        color: #fff;
      }
      .desc {
        display: inline-block;
        vertical-align: top;
        margin-top: 0.24rem;
        font-size: 0.28rem;
        line-height: 0.48rem;
        padding-left: 0.24rem;
        box-sizing: border-box;
        color: rgba(255, 255, 255, 0.4);
      }
    }
    .content-right {
      padding-top: 0.24rem;
      box-sizing: border-box;
      width: 2.1rem;
      font-size: 0.28rem;
      font-weight: 700;
      line-height: 0.48rem;
      text-align: center;
      color: rgba(255, 255, 255, 0.4);
      background-color: #2b333b;
    }
    .content-right.highlight {
      color: #fff;
      background-color: #00a0dc;
    }
  }
  .shopcart-list {
    position: absolute;
    top: 0;
    left: 0;
    z-index: -1;
    width: 100%;
    transform: translate3d(0, -100%, 0);
    .list-header {
      height: 0.8rem;
      line-height: 0.8rem;
      padding: 0 0.36rem;
      background-color: #f3f5f7;
      border-bottom: 0.02rem solid rgba(7, 17, 27, 0.1);
      .title {
        float: left;
        font-size: 0.28rem;
        color: rgb(7, 17, 27);
      }
      .empty {
        float: right;
        font-size: 0.24rem;
        color: rgb(0, 160, 220);
      }
    }
    .list-content {
      padding: 0 0.36rem;
      max-height: 4.34rem;
      background-color: #fff;
      overflow: hidden;
      box-sizing: border-box;
      .food {
        display: flex;
        padding: 0.24rem 0;
        box-sizing: border-box;
        height: 0.96rem;
        text-align: left;
        border-bottom: 1px solid rgba(7, 17, 27, 0.1);
        .name {
          flex: 1;
          font-size: 0.28rem;
          line-height: 0.48rem;
          color: rgb(7, 17, 27);
        }
        .price {
          margin: 0 0.24rem 0 0.36rem;
          font-size: 0.28rem;
          line-height: 0.48rem;
          color: rgb(240, 20, 20);
        }
      }
      .food:last-child {
        border-bottom: 0;
      }
    } 
  }
  .fold-enter-active,
  .fold-leave-active {
    transition: all .5s;
  }
  .fold-enter, 
  .fold-leave-active {
    transform: translate3d(0, 0, 0);
  }
}
.list-mask {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  z-index: 98;
  background-color: rgba(7, 17, 27, 0.6);
}
</style>
