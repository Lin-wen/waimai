<template>
  <transition name="move">
    <div v-show="showFlag" class="food" ref="food">
      <div class="food-content">
        <div class="img-header">
          <img :src="food.image" alt="">
          <div class="back" @click="hide">
            <i class="icon-arrow_lift"></i>
          </div>
        </div>
        <div class="content">
          <h1 class="title"> {{ food.name }} </h1>
          <div class="detail">
            <span class="sell-count">月售{{ food.sellCount }}份</span>
            <span class="rating">好评率{{ food.rating }}%</span>
          </div>
          <div class="price">
            <span class="now">
              ￥{{ food.price }}
            </span>
            <span v-show="food.oldPrice" class="oldprice">
              ￥{{ food.oldPrice }}
            </span>
          </div>
          <div class="cartcontrol-warpper">
            <cartcontrol :food="food"></cartcontrol>
          </div>
          <div class="buy" v-show="!food.count || food.count === 0" @click.stop.prevent="addFirst">加入购物车</div>
        </div>
        <split v-show="food.info"></split>
        <div class="info" v-show="food.info">
          <h1 class="title">商品信息</h1>
          <p class="text">{{ food.info }}</p>
        </div>
        <split></split>
        <div class="rating">
          <h1 class="title">商品评价</h1>
          <!-- @setSelectType="setSelectType" @setOnlycontent="setOnlycontent" 接收子组件$emit方法传过来的请求 -->
          <ratingselect :select-type="selectType" :only-content="onlyContent" :desc="desc" :ratings="food.ratings"  @setSelectType="setSelectType" @setOnlycontent="setOnlycontent"></ratingselect>
          <div class="rating-wrapper">
            <ul v-show="food.ratings && food.ratings.length">
              <li v-show="needShow(rating.rateType, rating.text)" v-for="rating in food.ratings" class="rating-item">
                <div class="user">
                  <span class="name">{{rating.username}}</span>
                  <img :src="rating.avatar" alt="" class="avatar">
                </div>
                <div class="time">{{rating.rateTime | formatDate}}</div>
                <p class="text">
                  <span :class="{'icon-thumb_up' : rating.rateType === 0, 'icon-thumb_down' : rating.rateType === 1}"></span>{{rating.text}}
                </p>
              </li>
            </ul>
            <div class="no-rating" v-show="!food.ratings || !food.ratings.length">暂无评价</div>
          </div>
        </div>
      </div>
    </div>
  </transition>
</template>

<script>
import Vue from 'vue'
import BScroll from 'better-scroll'
import cartcontrol from '../cartcontrol/cartcontrol'
import split from '../split/split'
import ratingselect from '../ratingselect/ratingselect'
import {formatDate} from '../../common/js/date'

const ALL = 2

export default {
  props: {
    food: {
      type: Object
    }
  },
  data () {
    return {
      showFlag: false,
      selectType: ALL,
      onlyContent: false,
      desc: {
        all: '全部',
        positive: '推荐',
        negative: '吐槽'
      }
    }
  },
  methods: {
    // 父组件可以调用子组件方法
    show () {
      this.showFlag = true
      this.selectType = ALL  // 初始化
      this.onlyContent = false  // *
      this.$nextTick(() => {
        if (!this.scroll) {
          this.scroll = new BScroll(this.$refs.food, {
            click: true
          })
        } else {
          this.scroll.refresh()
        }
      })
    },
    hide () {
      this.showFlag = false
    },
    addFirst (event) {
      if (!event._constructed) {
        return
      }
      Vue.set(this.food, 'count', 1)
      // this.food.count
    },
    needShow (type, text) {
      if (this.onlyContent && !text) {
        return false
      }
      if (this.selectType === ALL) {
        return true
      } else {
        return type === this.selectType
      }
    },
    setSelectType (type) {
      this.selectType = type
      this.$nextTick(() => {
        this.scroll.refresh()
      })
    },
    setOnlycontent () {
      this.onlyContent = !this.onlyContent
      this.$nextTick(() => {
        this.scroll.refresh()
      })
    }
  },
  filters: {
    formatDate (time) {
      let date = new Date(time)
      return formatDate(date, 'yyyy-MM-dd hh:mm')
    }
  },
  components: {
    cartcontrol,
    split,
    ratingselect
  }
}
</script>

<style lang="scss">
.food {
  position: fixed;
  top: 0;
  left: 0;
  bottom: 0.96rem;
  width: 100%;
  background: #fff;
  z-index: 89;
  .food-content {
    .img-header {
      position: relative;
      width: 100%;
      height: 0;
      padding-top: 100%;
      img {
        position: absolute;
        top: 0;
        left: 0;
        width: 100%;
        height: 100%;
      }
      .back {
        position: absolute;
        top: 0.1rem;
        left: 0.1rem;
        .icon-arrow_lift {
          display: block;
          padding: 0.2rem;
          color: #fff;
          font-size: 0.4rem;
        }
      }
    }
    .content {
      position: relative;
      padding: 0.36rem;
      box-sizing: border-box;
      text-align: left;
      .title {
        font-size: 0.28rem;
        font-weight: 700;
        color: rgb(7, 17, 27);
        line-height: 0.28rem;
      }
      .detail {
        margin-top: 0.16rem;
        font-size: 0;
        .sell-count, .rating {
          display: inline-block;
          font-size: 0.2rem;
          line-height: 0.2rem;
          font-weight: 700;
          color: rgb(147, 153, 159);
        }
        .rating {
          margin-left: 0.24rem;
        }
      }
      .price {
        margin-top: 0.36rem;
        font-size: 0;
        .now {
          font-size: 0.28rem;
          font-weight: 700;
          line-height: 0.48rem;
          color: rgb(240, 20, 20);
        }
        .oldprice {
          font-size: 0.2rem;
          font-weight: 700;
          line-height: 0.48rem;
          color: rgb(147, 153, 159);
        }
      }
      .cartcontrol-warpper {
        position: absolute;
        right: 0.36rem;
        bottom: 0.36rem;
      }
      .buy {
        position: absolute;
        right: 0.36rem;
        bottom: 0.38rem;
        height: 0.48rem;
        line-height: 0.48rem;
        z-index: 90;
        padding: 0 0.24rem;
        box-sizing: border-box;
        color: #fff;
        font-size: 0.2rem;
        border-radius: 0.24rem;
        background-color: rgb(0, 160, 220);
      }
    }
    .info {
      padding: 0.36rem;
      box-sizing: border-box;
      text-align: left;
      .title {
        font-size: 0.28rem;
        line-height: 0.48rem;
        font-weight: 500;
        color: rgb(7, 17, 27)
      }
      .text {
        margin-top: 0.12rem;
        font-size: 0.24rem;
        line-height: 0.48rem;
        font-weight: 200;
        color: rgb(77, 85, 93)
      }
    }
    .rating {
      padding-top: 0.36rem;
      box-sizing: border-box;
      text-align: left;
      .title {
        margin-left: 0.36rem;
        font-size: 0.28rem;
        line-height: 0.48rem;
        font-weight: 500;
        color: rgb(7, 17, 27);
      }
      .rating-wrapper {
        padding: 0 0.36rem;
        .rating-item {
          position: relative;
          padding: 0.32rem 0;
          border-bottom: 0.01rem solid rgba(7, 17, 27, 0.1);
          .user {
            position: absolute;
            right: 0;
            top: 0.32rem;
            line-height: 0.24rem;
            font-size: 0;
            .name {
              font-size: 0.2rem;
              line-height: 0.24rem;
              color: rgb(147, 153, 159);
              margin-right: 0.12rem;
            }
            .avatar {
              width: 0.24rem;
              height: 0.24rem;
              border-radius: 50%;
            }
          }
          .time {
            font-size: 0.2rem;
            line-height: 0.24rem;
            color: rgb(7, 17, 27);
          }
          .text {
            margin-top: 0.12rem;
            font-size: 0.24rem;
            line-height: 0.32rem;
            color: rgb(7, 17, 27);
            .icon-thumb_up {
              margin-right: 0.08rem;
              font-size: 0.24rem;
              line-height: 0.48rem;
              color: rgb(0, 160, 220);
            }
            .icon-thumb_down {
              margin-right: 0.08rem;
              font-size: 0.24rem;
              line-height: 0.48rem;
              color: rgb(147, 153, 159);
            }
          }
        }
        .no-rating {
          padding: 0.32rem 0;
          font-size: 0.24rem;
          line-height: 0.48rem;
          color: rgb(147, 153, 159);
        }
      }
    }
  }
}
.move-enter-active,
.move-leave-active {
  transition: all .2s linear;
  transform: translate3d(0, 0, 0);
}
.move-enter, 
.move-leave-active {
  transform: translate3d(100%, 0, 0);
}
</style>
