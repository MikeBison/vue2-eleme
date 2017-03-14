<template>
  <div class="goods">
    <div class="menu-wrapper">
      <ul>
        <li v-for="(item,index) in goods" class="menu-item" :class="{'current':currentIndex===index}" @click="selectMenu(index,$event)">
          <span class="text border-1px">
            <span v-show="item.type>0" class="icon" :class="classMap[item.type]"></span>
            {{item.name}}
          </span>
        </li>
      </ul>
    </div>
    <div class="foods-wrapper">
      <ul>
        <li v-for="item in goods" class="food-List food-list-hook">
          <h1 class="title" v-text="item.name"></h1>
          <ul>
            <li v-for="food in item.foods" class="food-item border-1px">
              <div class="icon">
                <img :src="food.icon" width="57" height="57">
              </div>
              <div class="content">
                <h2 class="name" v-text="food.name"></h2>
                <p class="desc" v-text="food.description"></p>
                <div class="extra">
                  <span class="count" v-text="'月售' + food.sellCount +'份'"></span><span v-text="'好评率'+food.rating+'%'"></span>
                </div>
                <div class="price">
                  <span v-text="'￥'+food.price" class="now"></span><span v-show="food.oldPrice" v-text="'￥'+food.oldPrice" class="old"></span>
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
  </div>
</template>

<script type="text/ecmascript-6">
import BScroll from 'better-scroll';
import shopcart from 'components/shopcart/shopcart.vue';
import cartcontrol from 'components/cartcontrol/cartcontrol.vue';
const ERR_OK = 0;
export default{
  props: {
    seller: {
        type: Object
    }
  },
  data () {
    return {
      goods : [],
      listHeight: [],
      scrollY: 0
    };
  },
  created () {
    this.classMap = ['decrease', 'discount', 'special', 'invoice', 'guarantee'];
    this.$http.get('/api/goods').then(res => {
      res = res.body;
      if (res.erron === ERR_OK) {
        this.goods = res.data;
        this.$nextTick(() => {
          this._initScroll();
          this._calculateHeight();
        });
      }
    });
  },
  components: {
    shopcart, cartcontrol
  },
  computed: {
    currentIndex () {
      for (let i = 0; i < this.listHeight.length; i++) {
        let height1 = this.listHeight[i];
        let height2 = this.listHeight[i + 1];
        if (!height2 || (this.scrollY >= height1 && this.scrollY < height2)) {
          return i;
        }
      }
      return 0;
    },
    selectFoods () {
      let foods = [];
      this.goods.forEach(good => {
        good.foods.forEach(food => {
          if (food.count) {
            foods.push(food);
          }
        });
      });
      return foods;
    }
  },
  methods: {
    _initScroll () {
      this.menuScroll = new BScroll(document.getElementsByClassName('menu-wrapper')[0], {
        click: true
      });
      this.foodsScroll = new BScroll(document.getElementsByClassName('foods-wrapper')[0], {
        probeType: 3,
        click: true
      });
      this.foodsScroll.on('scroll', (pos) => {
        this.scrollY = Math.abs(Math.round(pos.y));
      });
    },
    _calculateHeight () {
      let foodList = document.getElementsByClassName('food-list-hook');
      let height = 0;
      this.listHeight.push(height);
      for (let i = 0; i < foodList.length; i++) {
          let item = foodList[i];
          height += item.clientHeight;
          this.listHeight.push(height);
      }
    },
    selectMenu (index, event) {
      if (event._constructed) {
        return;
      }
      let foodList = document.getElementsByClassName('food-list-hook');
      let el = foodList[index];
      this.foodsScroll.scrollToElement(el, 300);
    }
  }
};
</script>

<style lang="stylus" rel="stylesheet/stylus">
  @import "../../common/stylus/mixin.styl"
  .goods
    display: flex
    position: absolute
    top: 174px
    bottom: 46px
    width: 100%
    overflow: hidden
    .menu-wrapper
      flex: 0 0 80px
      width: 80px;
      background: #f3f5f7
      .menu-item
        display : table
        height : 54px
        width : 80px
        line-height : 14px
        &.current
          position: relative
          margin-top: -1px
          z-index: 10
          background: #ffffff;
          font-weight: 700
          .text
            border-none()
        .icon
          display : inline-block
          width : 12px
          height : 12px
          vertical-align : top
          margin-right : 2px
          background-size : 12px 12px
          background-repeat : no-repeat
          &.decrease
            bg-img('decrease_3')
          &.discount
            bg-img('discount_3')
          &.guarantee
            bg-img('guarantee_3')
          &.invoice
            bg-img('invoice_3')
          &.special
            bg-img('special_3')
        .text
          font-size : 12px
          display : table-cell
          width: 80px
          padding: 0 12px
          border-1px(rgba(7,17,27,0.1))
          vertical-align: middle
    .foods-wrapper
      flex: 1
      .title
        padding-left : 14px
        height : 26px
        line-height : 26px
        border-left : 2px solid #d9dde1
        font-size : 12px
        color: rgb(147, 153, 159)
        background : #f3f5f7
      .food-item
        display: flex
        margin: 18px 0
        padding: 0 18px
        border-1px(rgba(7,17,27,0.1))
        padding-bottom: 18px
        &:last-child
          border-none()
          margin-bottom: 0px
        .icon
          flex: 0 0 57px
          margin-right: 10px
        .content
          flex: 1
          .name
            font-size: 14px
            margin: 2px 0 8px 0
            height : 14px
            line-height : 14px
            color: rgb(7, 17, 27)
          .desc
            line-height : 12px
            font-size : 10px
            margin-bottom : 8px
            color: rgb(147, 153, 159)
          .extra
            line-height: 12px
            font-size: 10px
            margin-bottom: 8px
            .count
              margin-right: 12px
          .price
            font-weight: 700
            line-height: 24px
            .now
              margin-right: 8px
              font-size: 14px
              color: rgb(240, 20, 20)
            .old
              text-decoration: line-through
              font-size: 10px
              color: grb(147, 153, 159)
          .cartcontrol-wrapper
            position: absolute
            right: 0
            bottom: 12px
</style>
