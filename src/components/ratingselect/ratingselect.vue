<template>
  <div class="reatingselect">
    <div class="rating-type">
      <span class="block positive" :class="{ 'active' : selectType === 2 }" @click="select(2, $event)">{{ desc.all }} <span class="count">{{ ratings.length }}</span></span>
      <span class="block positive" :class="{ 'active' : selectType === 0 }" @click="select(0, $event)">{{ desc.positive }} <span class="count">{{ positives.length }}</span></span>
      <span class="block negative" :class="{ 'active' : selectType === 1 }" @click="select(1, $event)">{{ desc.negative }} <span class="count">{{ negatives.length }}</span></span>
    </div>
    <div class="switch" :class="{ 'on' : onlyContent }" @click="toggleCount">
      <i class="icon-check_circle"></i>
      <span class="text">只看有内容的评价</span>
    </div>
  </div>
</template>

<script>
const POSITIVE = 0
const NEGATIVE = 1
const ALL = 2

export default {
  props: {
    ratings: {
      type: Array,
      default () {
        return []
      }
    },
    selectType: {
      type: Number,
      default: ALL
    },
    onlyContent: {
      type: Boolean,
      default: false
    },
    desc: {
      type: Object,
      default () {
        return {
          all: '全部',
          positive: '满意',
          negative: '不满意'
        }
      }
    }
  },
  computed: {
    positives () {
      return this.ratings.filter((rating) => {
        return rating.rateType === POSITIVE
      })
    },
    negatives () {
      return this.ratings.filter((rating) => {
        return rating.rateType === NEGATIVE
      })
    }
  },
  methods: {
    select (type, event) {
      if (!event._constructed) {
        return
      }
      this.selectType = type
      this.$emit('setSelectType', type) // 子组件通知父组件数值变化
    },
    toggleCount (event) {
      if (!event._constructed) {
        return
      }
      this.onlyContent = !this.onlyContent
      this.$emit('setOnlycontent')
    }
  }
}
</script>

<style lang="scss">
.reatingselect {
  .rating-type {
    margin: 0.24rem 0.36rem 0;
    padding-bottom: 0.36rem;
    font-size: 0;
    border-bottom: 0.01rem solid rgba(7, 17, 27, 0.1);
    .block {
      display: inline-block;
      margin-right: 0.16rem;
      font-size: 0.24rem;
      line-height: 0.32rem;
      padding: 0.16rem 0.24rem;
      box-sizing: border-box;
      border-radius: 0.02rem;
      .count {
        font-size: 0.16rem;
      }
    }
    .block.active {
      color: #fff;
    }
    .positive {
      color: rgb(77, 85, 93);
      background-color: rgba(0, 160, 220, 0.2);
    }
    .positive.active {
      background-color: rgb(0, 160, 220);
    }
    .negative {
      color: rgb(77, 85, 93);
      background-color: rgba(77, 85, 93, 0.2);
    }
    .negative.active {
      background-color: rgb(77, 85, 93);
    }
  }
  .switch {
    padding: 0.24rem 0.36rem;
    font-size: 0;
    border-bottom: 0.02rem solid rgba(7, 17, 27, 0.1);
    .icon-check_circle {
      display: inline-block;
      vertical-align: top;
      font-size: 0.48rem;
      line-height: 0.48rem;
      color: rgb(147, 153, 159);
    }
    .text {
      margin-left: 0.08rem;
      font-size: 0.24rem;
      line-height: 0.48rem;
      color: rgb(147, 153, 159);
    }
  }
  .switch.on .icon-check_circle {
    color: #00c850;
  }
}
</style>
