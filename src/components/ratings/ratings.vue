<template>
  <div class="ratings" ref="ratings">
    <div class="ratings-content">
      <div class="overview">
        <div class="overview-left">
          <h1 class="score">{{seller.score}}</h1>
          <div class="title">综合评分</div>
          <div class="rank">高于周边商家{{seller.rankRate}}%</div>
        </div>
        <div class="overview-right">
          <div class="score-wrapper">
            <span class="title">服务态度</span>
            <star :size="36" :score="seller.serviceScore"></star>
          </div>
          <div class="score-wrapper">
            <span class="title">商品评价</span>
            <star :size="36" :score="seller.foodScore"></star>
          </div>
          <div class="deliveryTime">
            <span class="title">送达时间</span>{{seller.deliveryTime}}分钟
          </div>
        </div>
      </div>
      <split></split>
      <ratingselect :select-type="selectType" :only-content="onlyContent" :desc="desc" :ratings="ratings"  @setSelectType="setSelectType" @setOnlycontent="setOnlycontent"></ratingselect>
      <div class="rating-wrapper">
        <ul v-show="ratings && ratings.length">
        <!-- <ul> -->
          <li v-for="rating in ratings" class="rating-item" v-show="needShow(rating.rateType, rating.text)">
            <div class="avatar">
              <img :src="rating.avatar" alt="">
            </div>
            <div class="content">
              <h1 class="name">{{rating.username}}</h1>
              <div class="star-wrapper">
                <star :size="24" :score="rating.score"></star>
                <span class="delivery" v-show="rating.deliveryTime">{{rating.deliveryTime}}分钟送达</span>
              </div>
              <p class="text">{{rating.text}}</p>
              <div class="recommend">
                <span :class="{'icon-thumb_up' : rating.rateType === 0, 'icon-thumb_down' : rating.rateType === 1}"></span>
                <span v-for="item in rating.recommend" class="recommend-item">{{item}}</span>
              </div>
              <div class="time">{{rating.rateTime | formatDate}}</div>
            </div>
          </li>
        </ul>
        <!-- <div class="no-rating" v-show="!ratings || !ratings.length">暂无评价</div> -->
      </div>
    </div>
  </div>
</template>

<script>
import BScroll from 'better-scroll'
import star from '../star/star'
import split from '../split/split'
import ratingselect from '../ratingselect/ratingselect'
import {formatDate} from '../../common/js/date'

const ALL = 2
const ERR_OK = 0

export default {
  props: {
    seller: {
      type: Object
    }
  },
  data () {
    return {
      ratings: [],
      selectType: ALL,
      onlyContent: false,
      desc: {
        all: '全部',
        positive: '满意',
        negative: '不满意'
      }
    }
  },
  created () {
    this.$http.get('/api/ratings').then(response => {
      response = response.body
      if (response.errno === ERR_OK) {
        // 获取数据from 'data.json'
        this.ratings = response.data
        this.$nextTick(() => {
          this.scroll = new BScroll(this.$refs.ratings, {
            click: true
          })
        })
      }
    })
  },
  methods: {
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
    star,
    split,
    ratingselect
  }
}
</script>

<style lang="scss">
.ratings {
  position: absolute;
  top: 3.48rem;
  left: 0;
  bottom: 0;
  width: 100%;
  overflow: hidden;
  .ratings-content {
    text-align: left;
    .overview {
      display: flex;
      padding: 0.36rem 0;
      box-sizing: border-box;
      .overview-left {
        flex: 0 0 2.75rem;
        width: 2.75rem;
        font-size: 0;
        text-align: center;
        border-right: 0.01rem solid rgba(7, 17, 27, 0.1);
        .score {
          font-size: 0.48rem;
          line-height: 0.56rem;
          color: rgb(255, 153, 0)
        }
        .title {
          margin-top: 0.12rem;
          font-size: 0.24rem;
          line-height: 0.24rem;
          font-weight: 700;
          color: rgb(7, 17, 27);
        }
        .rank {
          margin-top: 0.16rem;
          font-size: 0.2rem;
          line-height: 0.2rem;
          color: rgb(147, 153, 159);
        }
      }
      .overview-right {
        flex: 1;
        text-align: left;
        padding-left: 0.48rem;
        .score-wrapper {
          margin-bottom: 0.16rem;
          font-size: 0;
          .title {
            display: inline-block;
            vertical-align: top;
            font-size: 0.24rem;
            font-weight: 700;
            line-height: 0.36rem;
            color: rgb(7, 17, 27);
          }
          .star {
            display: inline-block;
            vertical-align: top;
            margin-left: 0.24rem;
          }
        }
        .deliveryTime {
          font-size: 0.24rem;
          line-height: 0.36rem;
          color: rgb(147, 153, 159);
          .title {
            font-weight: 700;
            color: rgb(7, 17, 27);
            margin-right: 0.24rem;
          }
        }
      }
    }
    .rating-wrapper {
      .rating-item {
        display: flex;
        margin: 0 0.36rem;
        padding: 0.36rem 0;
        border-bottom: 0.01rem solid rgba(7, 17,27, 0.1);
        .avatar {
          width: 0.56rem;
          height: 0.56rem;
          img {
            display: inline-block;
            vertical-align: top;
            width: 0.56rem;
            height: 0.56rem;
            border-radius: 50%;
          }
        }
        .content {
          flex: 1;
          position: relative;
          margin-left: 0.24rem;
          .name {
            font-size: 0.2rem;
            line-height: 0.24rem;
          }
          .star-wrapper {
            margin-top: 0.08rem;
            font-size: 0;
            .star, .delivery {
              display: inline-block;
              vertical-align: top;
            }
            .delivery {
              margin-left: 0.12rem;
              font-size: 0.2rem;
              font-weight: 200;
              line-height: 0.24rem;
              color: rgb(147, 153, 159);
            }
          }
          .text {
            margin-top: 0.12rem;
            font-size: 0.24rem;
            line-height: 0.36rem;
            color: rgb(7, 17, 27);
          }
          .recommend {
            margin-top: 0.16rem;
            font-size: 0;
            .icon-thumb_up {
              display: inline-block;
              vertical-align: top;
              font-size: 0.24rem;
              line-height: 0.32rem;
              color: rgb(0, 160, 220);
            }
            .icon-thumb_down {
              display: inline-block;
              vertical-align: top;
              font-size: 0.24rem;
              line-height: 0.32rem;
              color: rgb(147, 153, 159);
            }
            .recommend-item {
              display: inline-block;
              vertical-align: top;
              margin-left: 0.12rem;
              margin-bottom: 0.12rem;
              padding: 0 0.12rem;
              box-sizing: border-box;
              font-size: 0.18rem;
              line-height: 0.32rem;
              color: rgb(147, 153, 159);
              border: 0.01rem solid rgba(7, 17, 27, 0.1);
              border-radius: 0.02rem;
            }
          }
          .time {
            position: absolute;
            top: 0;
            right: 0;
            font-size: 0.2rem;
            font-weight: 200;
            line-height: 0.24rem;
            color: rgb(147, 153, 159);
          }
        }
      }
    }
  }
}
</style>
