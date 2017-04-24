<template>
  <div class="cartcontrol">
    <!-- @click.stop.prevent 阻止鼠标点击冒泡 -->
    <div class="cart-decrease" v-show="food.count > 0" @click.stop.prevent="decreaseCart">
      <span></span><i class="icon-remove_circle_outline"></i>
    </div>
    <div class="cart-count" v-show="food.count > 0">
      {{ food.count }}
    </div>
    <div class="cart-add">
      <i class="icon-add_circle" @click.stop.prevent="addCart"></i>
    </div>
  </div>
</template>

<script>
import Vue from 'vue'

export default {
  props: {
    food: {
      type: Object
    }
  },
  methods: {
    addCart (event) {
      if (!event._constructed) {
        return
      }

      if (!this.food.count) {
        Vue.set(this.food, 'count', 1)
      } else {
        this.food.count++
      }
    },
    decreaseCart (event) {
      if (!event._constructed) {
        return
      }
      if (this.food.count > 0) {
        this.food.count--
      }
    }
  },
  created () {
    // console.log(this.food)
  }
}
</script>

<style lang="scss">
.cartcontrol {
  font-size: 0;
  .cart-decrease, .cart-add{
    display: inline-block;
    font-size: 0.48rem;
    line-height: 0.48rem;
    color: #00a0dc;
  }
  .cart-count {
    display: inline-block;
    width: 0.48rem;
    height: 0.48rem;
    font-size: 0.32rem;
    line-height: 0.48rem;
    text-align: center;
    vertical-align: top;
    color: rgb(147, 153, 159);
  }
}
</style>
