<template>
  <div id="detail">
    <detail-nav-bar class="detail-nav" @titleClick="titleClick" ref="nav"/>
    <scroll class="content" ref="scroll" @scroll="contentScroll" :probe-type="3">
      <detail-swiper :top-images="topImages"/>
      <detail-base-info :goods="goodsInfo"/>
      <detail-shop-info :shop="shopInfo"/>
      <detail-goods-info :detail-info="detailInfo" @imageLoad="imageLoad"/>
      <detail-param-info :paramInfo="itemParams" ref="params"/>
      <detail-comment-info :comment-info="commentInfo" ref="comment"/>
      <goods-list :goods="recommends" ref="recommend"/>
    </scroll>
    <detail-bottom-bar @addCart="addToCart"/>
    <back-top @click.native="backClick" v-show="isShowBackTop"/>
  </div>
</template>

<script>
  import DetailNavBar from "./childComps/DetailNavBar";
  import DetailSwiper from "./childComps/DetailSwiper";
  import DetailBaseInfo from "./childComps/DetailBaseInfo";
  import DetailShopInfo from "./childComps/DetailShopInfo";
  import DetailGoodsInfo from './childComps/DetailGoodsInfo';
  import DetailParamInfo from "./childComps/DetailParamInfo";
  import DetailCommentInfo from "./childComps/DetailCommentInfo";
  import DetailBottomBar from "./childComps/DetailBottomBar";

  import BScroll from 'components/common/scroll/Scroll';

  import {getDetail, getRecommend, Goods, Shop, GoodsParam} from "network/detail";
  import Scroll from "@/components/common/scroll/Scroll";

  import GoodsList from "components/content/goods/GoodsList";

  import {itemListenerMixin, backTopMixin} from 'common/mixin';
  import {debounce} from 'common/utils';

  import {mapActions} from 'vuex'

  export default {
    name: "Detail",
    components: {
      Scroll,
      DetailNavBar,
      DetailSwiper,
      DetailBaseInfo,
      DetailShopInfo,
      DetailGoodsInfo,
      DetailParamInfo,
      DetailCommentInfo,
      DetailBottomBar,
      GoodsList,
      BScroll
    },
    mixins: [itemListenerMixin, backTopMixin],
    data() {
      return {
        iid: null,
        topImages: [],
        goodsInfo: {},
        shopInfo: {},
        detailInfo: {},
        itemParams: {},
        commentInfo: {},
        recommends: [],
        themeTopY: [],
        getThemeTopY: null,
        currentIndex: 0
      }
    },
    methods: {
      ...mapActions(['addCart']),
      imageLoad() {
        this.$refs.scroll.refresh();
        this.getThemeTopY()
      },
      titleClick(index) {
        this.$refs.scroll.scrollTo(0, -this.themeTopY[index], 200)
      },
      contentScroll(position) {
        const positionY = -position.y;
        let length = this.themeTopY.length;
        for (let i = 0; i < length; i++) {
          if (this.currentIndex !== i && ((i < length - 1 && positionY >= this.themeTopY[i] && positionY < this.themeTopY[i + 1])
            || (i === length - 1 && positionY >= this.themeTopY[i]))) {
            this.currentIndex = i;
            this.$refs.nav.currentIndex = this.currentIndex
          }
        }

        //判断BackTop是否显示
        this.listenerShowBackTop(position);
      },
      addToCart() {
        //获取要展示的信息
        const product = {};
        product.image = this.topImages[0];
        product.title = this.goodsInfo.title;
        product.desc = this.goodsInfo.desc;
        product.price = this.goodsInfo.realPrice;
        product.iid = this.iid;
        //将商品添加到购物车
        this.addCart(product).then(res => {
          this.$toast.show(res, 1500)
        })
      }
    },
    created() {
      //保存传入的iid
      this.iid = this.$route.params.iid;

      //根据iid请求详情数据
      getDetail(this.iid).then(res => {
        const data = res.result;
        //获取顶部的轮播图片数据
        this.topImages = res.result.itemInfo.topImages;
        //获取商品信息
        this.goodsInfo = new Goods(data.itemInfo, data.columns, data.shopInfo.services);
        //获取店铺信息
        this.shopInfo = new Shop(data.shopInfo);
        //获取商品详情信息
        this.detailInfo = data.detailInfo;
        //商品参数的信息
        this.itemParams = new GoodsParam(data.itemParams.info, data.itemParams.rule);
        //评论信息
        if (data.rate.cRate !== 0) {
          this.commentInfo = data.rate.list[0];
        }
      });

      //请求推荐数据
      getRecommend().then(res => {
        this.recommends = res.data.list;
      });

      //给getThemeTopY赋值
      this.getThemeTopY = debounce(() => {
        this.themeTopY = [];
        this.themeTopY.push(0);
        this.themeTopY.push(this.$refs.params.$el.offsetTop);
        this.themeTopY.push(this.$refs.comment.$el.offsetTop);
        this.themeTopY.push(this.$refs.recommend.$el.offsetTop);
      }, 100)
    },
    mounted() {
    },
    destroyed() {
      this.$bus.$off('itemImgLoad', this.itemImgListener)
    }
  }
</script>

<style scoped>
  #detail {
    height: 100vh;
  }

  .detail-nav {
    position: relative;
    z-index: 9;
    background-color: #fff;
  }

  .content {
    height: calc(100% - 44px - 49px);
  }
</style>
