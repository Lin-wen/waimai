<template>
  <div class="seller" ref="seller">
    <div class="seller-content">
      <div class="overview">
        <h1 class="title">{{seller.name}}</h1>
        <div class="star-wrapper">
          <star :size="36" :score="seller.score"></star>
          <span class="sellcount">月售{{seller.sellCount}}份</span>
        </div>
        <div class="delivery">
          <div class="delivery-item">
            <span class="title">起送价</span>
            {{seller.minPrice}}
          </div>
          <div class="delivery-item">
            <span class="title">商家配送</span>
            {{seller.deliveryPrice}}
          </div>
          <div class="delivery-item">
            <span class="title">平均配送时间</span>
            {{seller.deliveryTime}}
          </div>
        </div>
        <div class="favorite" @click="toggleFavorite">
          <i class="icon-favorite" :class="{active: favorite}"></i>
          <span class="text">{{favoriteText}}</span>
        </div>
      </div>
      <split></split>
      <div class="bulletin">
        <h1 class="title">公告与活动</h1>
        <p class="text">{{seller.bulletin}}</p>
        <ul class="supports">
          <li v-for="(item,index) in seller.supports" class="supports-item">
            <span class="icon" :class="classMap[seller.supports[index].type]"></span>
            <span class="text">
              {{ seller.supports[index].description }}
            </span>
          </li>
        </ul>
      </div>
      <split></split>
      <div class="pics">
        <h1 class="title">商家实景</h1>
        <div class="pic-wrapper" ref="picWrapper">
          <ul class="pic-list" ref="picList">
            <li v-for="item in seller.pics" class="pic-item">
              <img :src="item">
            </li>
          </ul>
        </div>
      </div>
      <split></split>
      <div class="infos">
        <h1 class="title">商家信息</h1>
        <ul>
          <li v-for="item in seller.infos" class="infos-item">{{item}}</li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script>
import BScroll from 'better-scroll'
import star from '../star/star'
import split from '../split/split'
import Store from '../../common/js/store' // H5离线存储

export default {
  props: {
    seller: {
      type: Object
    }
  },
  data () {
    return {
      favorite: (() => {
        return Store.load(this.seller.id, 'favorite', false)
      })()
    }
  },
  computed: {
    favoriteText () {
      return this.favorite ? '已收藏' : '收藏'
    }
  },
  watch: {
    'seller' () {
      this.$nextTick(() => {
        this._initScroll()
        this._initPicScroll()
      })
    }
  },
  mounted () {
    this.$nextTick(() => {
      this._initScroll()
      this._initPicScroll()
    })
  },
  methods: {
    _initScroll () {
      // console.log(1)
      if (!this.scroll) {
        this.scroll = new BScroll(this.$refs.seller, {
          click: true
        })
      } else {
        this.scroll.refresh()
      }
    },
    _initPicScroll () {
      // console.log(2)
      if (this.seller.pics) {
        let picWidth = 2.4
        let margin = 0.12
        let width = (picWidth + margin) * this.seller.pics.length - margin
        this.$refs.picList.style.width = width * 100 + 'px'
        this.$nextTick(() => {
          if (!this.picScroll) {
            this.picScroll = new BScroll(this.$refs.picWrapper, {
              scrollX: true,
              eventPassthrough: 'vertical'
            })
          } else {
            this.picScroll.refresh()
          }
        })
      }
    },
    toggleFavorite (event) {
      if (!event._constructed) {
        return
      }
      this.favorite = !this.favorite
      Store.save(this.seller.id, 'favorite', this.favorite)
    }
  },
  created () {
    this.classMap = ['decrease', 'discount', 'special', 'invoice', 'guarantee']
  },
  components: {
    star,
    split
  }
}
</script>

<style lang="scss">
.seller {
  position: absolute;
  top: 3.48rem;
  left: 0;
  bottom: 0;
  width: 100%;
  overflow: hidden;
  .seller-content {
    text-align: left;
    .overview {
      position: relative;
      padding: 0.36rem;
      box-sizing: border-box;
      .title {
        margin-bottom: 0.24rem;
        font-size: 0.28rem;
        line-height: 0.28rem;
        color: rgb(7, 17, 27);
      }
      .star-wrapper {
        margin-top: 0.16rem;
        font-size: 0;
        .star {
          display: inline-block;
          vertical-align: top;
        }
        .sellcount {
          display: inline-block;
          margin-left: 0.24rem;
          vertical-align: top;
          font-size: 0.2rem;
          line-height: 0.36rem;
          color: rgb(77, 85, 93);
        }
      }
      .delivery {
        display: flex;
        margin-top: 0.36rem;
        padding: 0.36rem 0;
        box-sizing: border-box;
        // font-size: 0;
        border-top: 1px solid rgba(7, 17, 27, 0.1);
        .delivery-item {
          flex: 1;
          font-size: 0.48rem;
          line-height: 0.48rem;
          font-weight: 200;
          color: rgb(7, 17, 27);
          text-align: center;
          border-right: 1px solid rgba(7, 17, 27, 0.1);
          .title {
            display: block;
            margin-bottom: 0.08rem;
            font-size: 0.2rem;
            line-height: 0.2rem;
            color: rgb(147, 153, 159);
          }
        }
        .delivery-item:last-child {
          border-right: 0;
        }
      }
      .favorite {
        position: absolute;
        right: 0.36rem;
        top: 0.36rem;
        width: 0.6rem;
        height: 0.84rem;
        font-size: 0;
        .icon-favorite {
          display: block;
          font-size: 0.48rem;
          line-height: 0.48rem;
          text-align: center;
          color: #d4d6d9;
        }
        .active {
          color: rgb(240, 20, 20);
        }
        .text {
          display: block;
          margin-top: 0.08rem;
          font-size: 0.2rem;
          line-height: 0.36rem;
          text-align: center;
          color: rgb(77, 85, 93);
        }
      }
    }
    .bulletin {
      padding: 0.36rem;
      box-sizing: border-box;
      .title {
        margin-bottom: 0.24rem;
        font-size: 0.28rem;
        line-height: 0.28rem;
        color: rgb(7, 17, 27);
      }
      .text {
        margin-left: 0.24rem;
        font-size: 0.24rem;
        line-height: 0.48rem;
        font-weight: 200;
        color: rgb(240, 20, 20);
      }
      .supports {
        margin-top: 0.32rem;
        .supports-item {
          padding: 0.32rem 0.24rem;
          box-sizing: border-box;
          font-size: 0;
          border-top: 0.01rem solid rgba(7, 17, 27, 0.1);
          .icon {
            display: inline-block;
            vertical-align: top;
            width: 0.32rem;
            height: 0.32rem;
          }
          .text {
            display: inline-block;
            vertical-align: top;
            font-size: 0.24rem;
            font-weight: 200;
            line-height: 0.32rem;
            color: rgb(7, 17, 27);
          }
        }
      }
    }
    .pics {
      padding: 0.36rem;
      box-sizing: border-box;
      .title {
        margin-bottom: 0.24rem;
        font-size: 0.28rem;
        line-height: 0.28rem;
        color: rgb(7, 17, 27);
      }
      .pic-wrapper {
        width: 100%;
        // height: 1.8rem;
        overflow: hidden;
        white-space: nowrap;
        .pic-list {
          font-size: 0;
          .pic-item {
            display: inline-block;
            margin-right: 0.12rem;
            width: 2.4rem;
            height: 1.8rem;
            img {
              width: 2.4rem;
              height: 1.8rem;
            }
          }
          .pic-item:last-child {
            margin-right: 0;
          }
        }
      }
    }
    .infos {
      padding: 0.36rem;
      box-sizing: border-box;
      .title {
        margin-bottom: 0.24rem;
        font-size: 0.28rem;
        line-height: 0.28rem;
        color: rgb(7, 17, 27);
      }
      .infos-item {
        padding: 0.32rem 0.24rem;
        box-sizing: border-box;
        font-size: 0.24rem;
        color: rgb(7, 17, 27);
        border-top: 0.01rem solid rgba(7, 17, 27, 0.1);
      }
    }
  }
}
</style>
