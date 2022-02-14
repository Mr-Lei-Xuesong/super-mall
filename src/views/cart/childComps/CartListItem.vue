<template>
  <div class="cart-list-item">
    <div class="item-seletor">
      <check-button :is-checked="cartList.checked"
                    @click.native="checkClick"/>
    </div>
    <div class="item-img">
      <img :src="cartList.image" alt="goods_img">
    </div>
    <div class="item-info">
      <div class="item-title">{{cartList.title}}</div>
      <div class="item-desc">{{cartList.desc}}</div>
      <div class="item-bottom">
        <div class="item-price left">{{cartList.price}}</div>
        <div class="item-count right">x{{cartList.count}}</div>
      </div>
    </div>
  </div>
</template>

<script>
  import CheckButton from "components/content/checkButton/CheckButton";

  export default {
    name: "CartListItem",
    props: {
      cartList: {
        type: Object,
        default() {
          return {}
        }
      }
    },
    components: {
      CheckButton,
    },
    methods: {
      checkClick() {
        this.cartList.checked = !this.cartList.checked
      }
    },
  }
</script>

<style scoped>
  .cart-list-item {
    width: 100%;
    padding: 5px;
    display: flex;
    font-size: 0;
    border-bottom: 2px solid rgba(100, 100, 100, .2);

  }

  .item-seletor {
    width: 20px;
    display: flex;
    justify-content: center;
    align-items: center;
  }

  .item-img {
    padding: 5px;
  }

  .item-img img {
    width: 80px;
    height: 100px;
    border-radius: 5px;
    display: block;
  }

  .item-info {

    padding: 5px 10px;
    overflow: hidden;
    font-size: 17px;
    color: #333;
    position: relative;
  }

  .item-info .item-desc {
    font-size: 14px;
    color: #666;
    margin-top: 15px;
  }

  .item-title, .item-desc {
    overflow: hidden;
    white-space: nowrap;
    text-overflow: ellipsis;
  }

  .item-bottom {

    margin-top: 10px;
    position: absolute;
    bottom: 10px;
    left: 10px;
    right: 10px;
  }

  .item-info .item-price {
    color: var(--color-high-text);
    /* font-size: 20px; */
    font-weight: bold;
  }

</style>
