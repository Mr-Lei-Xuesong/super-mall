<template>
  <div id="detail">
    <detail-nav-bar class="detail-nav"/>
    <scroll class="content" ref="scroll">
      <detail-swiper :top-images="topImages"/>
      <detail-base-info :goods="goodsInfo"/>
      <detail-shop-info :shop="shopInfo"/>
      <detail-goods-info :detail-info="detailInfo" @imageLoad="imageLoad"/>
      <detail-param-info :paramInfo="itemParams"/>
      <detail-comment-info :comment-info="commentInfo"/>
      <goods-list :goods="recommends"/>
    </scroll>
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

  import BScroll from 'components/common/scroll/Scroll'

  import {getDetail, getRecommend, Goods, Shop, GoodsParam} from "network/detail";
  import Scroll from "@/components/common/scroll/Scroll";

  import GoodsList from "components/content/goods/GoodsList";

  import {itemListenerMixin} from 'common/mixin'

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
      GoodsList,
      BScroll
    },
    mixins: [itemListenerMixin],
    data() {
      return {
        iid: null,
        topImages: [],
        goodsInfo: {},
        shopInfo: {},
        detailInfo: {},
        itemParams: {},
        commentInfo: {},
        recommends: []
      }
    },
    methods: {
      imageLoad() {
        this.$refs.scroll.refresh()
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
        this.itemParams = new GoodsParam(data.itemParams.info, data.itemParams.rule)
        //评论信息
        if (data.rate.cRate !== 0) {
          this.commentInfo = data.rate.list[0];
        }
      });

      //请求推荐数据
      getRecommend().then(res => {
        this.recommends = res.data.list;
      })
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
    height: calc(100% - 44px);
  }
</style>
