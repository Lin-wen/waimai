<template>
  <div class="star" :class="starType">
    <span v-for="itemClass in itemClasses" :class="itemClass" class="star-item"></span>
  </div>
</template>

<script>

const LENGTH = 5
const CLS_ON = 'on'
const CLS_HALF = 'half'
const CLS_OFF = 'off'

export default {
  props: {
    size: {
      type: Number
    },
    score: {
      type: Number
    }
  },
  computed: {
    starType () {
      return 'star-' + this.size
    },
    itemClasses () {
      let result = []
      let score = Math.floor(this.score * 2) / 2
      let hasDecimal = score % 1 !== 0
      let integer = Math.floor(score)
      for (let i = 0; i < integer; i++) {
        result.push(CLS_ON)
      }
      if (hasDecimal) {
        result.push(CLS_HALF)
      }
      while (result.length < LENGTH) {
        result.push(CLS_OFF)
      }
      return result
    }
  }
}
</script>

<style lang="scss">
.star {
  font-size: 0;
  .star-item {
  display: inline-block;
  background-repeat: no-repeat;
}
}
.star-48 .star-item {
  width: 0.4rem;
  height: 0.38rem;
  margin-right: 0.44rem;
  background-size: 0.4rem 0.4rem;
}
.star-48 .star-item:last-child {
  margin-right: 0;
}
.star-48 .on {
  background: url(star48_on@2x.png) no-repeat;
}
.star-48 .half {
  background: url(star48_half@2x.png) no-repeat;
}
.star-48 .off {
  background: url(star48_off@2x.png) no-repeat;
}
.star-36 .star-item {
  width: 0.3rem;
  height: 0.3rem;
  margin-right: 0.15rem;
  background-size: 0.3rem 0.3rem;
}
.star-36 .star-item:last-child {
  margin-right: 0;
}
.star-36 .on {
  background: url(star36_on@2x.png) no-repeat;
}
.star-36 .half {
  background: url(star36_half@2x.png) no-repeat;
}
.star-36 .off {
  background: url(star36_off@2x.png) no-repeat;
}
.star-24 .star-item {
  width: 0.2rem;
  height: 0.2rem;
  margin-right: 0.1rem;
  background-size: 0.2rem 0.2rem;
}
.star-24 .star-item:last-child {
  margin-right: 0;
}
.star-24 .on {
  background: url(star24_on@2x.png) no-repeat;
}
.star-24 .half {
  background: url(star24_half@2x.png) no-repeat;
}
.star-24 .off {
  background: url(star24_off@2x.png) no-repeat;
}
</style>
