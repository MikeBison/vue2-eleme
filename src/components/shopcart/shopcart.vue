<template>
  <div class="shopcart">
    <div class="content">
      <div class="content-left">
        <div class="logo-wrapper">
          <div class="logo" :class="{'highlight':totalCount>0}">
            <i class="icon-shopping_cart" :class="{'highlight':totalCount>0}"></i>
          </div>
          <div class="num" v-text="totalCount" v-show="totalCount>0"></div>
        </div>
        <div class="price" v-text="'￥' + totalPrice" :class="{'highlight':totalPrice>0}"></div>
        <div class="desc" v-text="'另需配送费 ￥'+deliveryPrice+' 元'"></div>
      </div>
      <div class="content-right">
        <div class="pay" v-text="payDesc" :class="totalPrice>=minPrice?'enough':'not-enough'"></div>
      </div>
    </div>
  </div>
</template>

<script type="text/ecmascript-6">
  export default {
    props:{
      deliveryPrice: {
        type: Number,
        default: 0
      },
      minPrice: {
        type: Number,
        default: 0
      },
      selectFoods: {
        type: Array,
        default () {
          return [{
            price: 20,
            count: 1
          }];
        }
      }
    },
    computed: {
      totalPrice () {
        let total = 0;
        this.selectFoods.forEach(food => {
          total += food.price * food.count;
        });
        return total;
      },
      totalCount () {
        let count = 0;
        this.selectFoods.forEach(food => {
          count += food.count;
        });
        return count;
      },
      payDesc () {
        if (this.totalPrice === 0) {
          return `￥ ${this.minPrice}元起送`;
        } else if (this.totalPrice < this.minPrice) {
          let diff = this.minPrice - this.totalPrice;
          return `还差￥${diff}元起送`;
        } else {
          return '去结算';
        }
      }
    }
  };
</script>

<style lang="stylus" rel="stylesheet/stylus">
.shopcart
  height: 48px
  width: 100%
  position: fixed
  bottom: 0
  left: 0
  z-index: 50
  .content
    background: #141d27
    color: rgba(255,255,255,0.4)
    display: flex;
    .content-left
      flex: 1
      font-size: 0
      .logo-wrapper
        display: inline-block
        position: relative
        margin: 0 12px
        padding: 6px
        top: -10px
        width: 56px
        height: 56px
        box-sizing: border-box
        vertical-align: top
        border-radius: 50%
        background-color: #141d27
        .logo
          width: 100%;
          height: 100%
          border-radius: 50%
          background: #2b343c
          text-align: center
          &.highlight
            background: rgb(0, 160, 220)
          .icon-shopping_cart
            font-size: 24px
            color: #80858a
            line-height: 44px
            &.highlight
              color: #ffffff
        .num
          position: absolute
          top: 0
          right: 0
          width: 24px
          height: 16px
          line-height: 16px
          text-align: center
          border-radius: 16px
          font-size: 9px
          font-weight: 700
          color: #ffffff
          background: rgb(240,20,20)
          box-shadow: 0 4px 8px 0 rgba(0,0,0,0.4)
      .price
        display: inline-block
        vertical-align: top
        line-height: 24px
        margin-top: 12px
        box-sizing: border-box
        padding-right: 12px
        border-right: 1px solid rgba(255,255,255,0.1)
        font-size: 16px
        font-weight: 700
        &.highlight
          color: #ffffff
      .desc
        display: inline-block
        vertical-align : top
        line-height: 24px
        margin: 12px 0 0 12px
        font-size: 10px
    .content-right
      flex: 0 0 105px
      width: 105px
      .pay
        height: 48px
        line-height: 48px
        text-align: center
        font-size: 12px
        font-weight: 700
        background: #2b333b
        &.not-enough
          background: #2b333b
        &.enough
          background: #00b43c
          color: #ffffff
</style>
