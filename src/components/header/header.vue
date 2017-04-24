<template>
  <div class="header">
    <div class="content-wrapper">
      <div class="avatar">
        <img :src="seller.avatar" alt="">
      </div>
      <div class="content">
        <div class="title">
          <span class="brand"></span>
          <span class="name">{{ seller.name }}</span>
        </div>
        <div class="description">
          {{ seller.description }}/{{ seller.deliveryTime }}分钟送达
        </div>
        <div v-if="seller.supports" class="support">
          <span class="icon" :class="classMap[seller.supports[0].type]"></span>
          <span class="text">
            {{ seller.supports[0].description }}
          </span>
        </div>
      </div>
      <div v-if="seller.supports" v-on:click="showDetail" class="support-count">
        <span class="count">{{ seller.supports.length }}个</span>
        <i class="icon-keyboard_arrow_right"></i>
      </div>
    </div>
    <div class="bulletin-wrapper" v-on:click="showDetail">
      <span class="icon"></span>
      <span class="bulletin">
        {{ seller.bulletin }}
      </span>
      <!-- <i class="icon-keyboard_arrow_right"></i> -->
    </div>
    <div class="bg">
      <img :src="seller.avatar" width="100%" alt="">
    </div>
    <transition name="fade">
      <div v-show="detailShow" class="detail">
        <div class="detail-bg"></div>
        <div class="detail-wrapper clearfix">
          <div class="detail-main">
            <h1 class="name">{{ seller.name }}</h1>
            <div class="star-wrapper">
              <star :size="48" :score="seller.score"></star>
            </div>
            <div class="title">
              <div class="line"></div>
              <div class="text">优惠信息</div>
              <div class="line"></div>
            </div>
            <ul v-if="seller.supports" class="supports">
              <li v-for="(item,index) in seller.supports">
                <span class="icon" :class="classMap[seller.supports[index].type]"></span>
                <span class="text">
                  {{ seller.supports[index].description }}
                </span>
              </li>
            </ul>
            <div class="title">
              <div class="line"></div>
              <div class="text">商家公告</div>
              <div class="line"></div>
            </div>
            <p class="bulletin-text">{{ seller.bulletin }}</p>
          </div>
        </div>
        <div class="detail-close" @click="showDetail">
          <i class="icon-close"></i>
        </div>
      </div>
    </transition>
  </div>
</template>

<script>
import star from '../star/star'
export default {
  props: {
    // 接收APP.vue 'v-header'组件传过来的对象
    seller: {
      type: Object
    }
  },
  data: function () {
    return {
      detailShow: false
    }
  },
  methods: {
    showDetail: function () {
      this.detailShow = !this.detailShow
    }
  },
  created () {
    this.classMap = ['decrease', 'discount', 'special', 'invoice', 'guarantee']
  },
  components: {
    star
  }
}
</script>

<style lang="scss">
.header{
  position: relative;
  height: 2.68rem;
  text-align: left;
  color: #fff;
  background-color: rgba(7, 17, 27, 0.5);
  .content-wrapper {
    position: relative;
    height: 2.12rem;
    padding: 0.48rem 0.24rem 0.36rem 0.48rem;
    box-sizing: border-box;
    .avatar {
      float: left;
      img{
        width: 1.28rem;
        height: 1.28rem;
        border-radius: 0.08rem; 
      }      
    }
    .content {
      float: left;
      padding: 0;
      margin-left: 0.32rem;
      .title {
        margin-top: 0.04rem;
        height: 0.36rem;
        font-size: 0;
        span {
          display: inline-block;
          vertical-align: top;
        }
        .brand {
          width: 0.6rem;
          height: 0.36rem;
          background: url(brand@2x.png) no-repeat;
        }
        .name {
          margin-left: 0.12rem;
          font-size: 0.32rem;
          font-weight: bold;
          line-height: 0.36rem;
          color: rgb(255, 255, 255);
        }
      }
      .description {
        margin-top: 0.16rem;
        font-size: 0.24rem;
        font-weight: 200;
        line-height: 0.24rem;
        color: rgb(255, 255, 255);
      }
      .support {
        margin-top: 0.2rem;
        font-size: 0.2rem;
        font-weight: 200;
        line-height: 0.24rem;
        color: rgb(255, 255, 255);
        span {
          display: inline-block;
          vertical-align: top;
        }
        .icon {
          width: 0.24rem;
          height: 0.24rem;
        }
      }
    }
    .support-count {
      padding: 0.14rem 0.16rem;
      position: absolute;
      right: 0.48rem;
      bottom: 0.36rem;
      height: 0.48rem;
      font-size: 0.2rem;
      line-height: 0.24rem;
      border-radius: 50%;
      box-sizing: border-box;
      background-color: rgba(0, 0, 0, 0.2);
    }
  }
  .bulletin-wrapper {
    padding: 0 0.24rem;
    height: 0.56rem;
    font-size: 0.2rem;
    line-height: 0.56rem;
    background-color: rgba(7, 17, 27, 0.2);
    .icon {
      display: block;
      float: left;
      margin-top: 0.16rem;
      width: 0.44rem;
      height: 0.24rem;
      background: url(bulletin@2x.png) no-repeat;
    }
    .bulletin {
      display: block;
      margin-left: 0.08rem;
      white-space: nowrap;
      overflow: hidden;
      text-overflow: ellipsis;
    }
  }
  .bg {
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    z-index: -1;
    filter: blur(0.1rem);
    overflow: hidden;
  }
  .detail {
    position: fixed;
    top: 0;
    left: 0;
    z-index: 100;
    width: 100%;
    height: 100%;
    overflow: auto;
    transition: all 0.5s;
    background: url(ms.png) rgba(7, 17, 27, 0.8);
    background-size: 100% 100%;
    .detail-wrapper {
      width: 100%;
      min-height: 100%;
      .detail-main {
        margin-top: 1.28rem;
        padding-bottom: 1.28rem;
        .name {
          font-size: 0.32rem;
          font-weight: 700;
          line-height: 0.32rem;
          text-align: center;
        }
        .star-wrapper {
          margin: 0.32rem auto 0.36rem;
          text-align: center;
        }
        .title {
          display: flex;
          width: 80%;
          margin: 0 auto;
          .line {
            flex: 1;
            position: relative;
            top: -0.12rem;
            border-bottom: 1px solid rgba(255, 255, 255, 0.2);
          }
          .text {
            padding: 0 0.24rem;
            font-size: 0.28rem;
          }
        }
        .supports {
          margin: 0.48rem auto 0.56rem;
          padding: 0 0.24rem;
          box-sizing: border-box;
          width: 80%;
          font-size: 0.24rem;
          li {
            margin-bottom: 0.24rem;
            .icon {
              display: inline-block;
              margin-right: 0.12rem;
              width: 0.24rem;
              height: 0.24rem;
            }
          }
        }
        .bulletin-text {
          margin: 0.48rem auto 0;
          padding: 0 0.24rem;
          box-sizing: border-box;
          width: 80%;
          font-size: 0.24rem;
          line-height: 0.48rem;
        }
      }
    }
    .detail-close {
      position: relative;
      width: 0.64rem;
      height: 0.645rem;
      margin: -1.28rem auto 0;
      clear: both;
      font-size: 0.64rem;
    }
  }
}
.decrease {
  background: url(decrease_1@2x.png) no-repeat;
}
.discount {
  background: url(discount_1@2x.png) no-repeat;
}
.special {
  background: url(special_1@2x.png) no-repeat;
}
.invoice {
  background: url(invoice_1@2x.png) no-repeat;
}
.guarantee {
  background: url(guarantee_1@2x.png) no-repeat;
}
.fade-enter-active, .fade-leave-active {
  transition: opacity .5s
}
.fade-enter, .fade-leave-active {
  opacity: 0
}
</style>
