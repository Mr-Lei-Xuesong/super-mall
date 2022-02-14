<template>
  <div class="check-btn" :class="{check: isChecked}">
    <img src="~assets/img/cart/tick.svg" alt="">
  </div>
</template>

<script>
  export default {
    name: "CheckButton",
    props: {
      isChecked: {
        type: Boolean,
        default: false,
      }
    }
  }
</script>

<style scoped>
  .check-btn {
    width: 20px;
    height: 20px;
    border-radius: 50%;
    text-align: center;
    border: 2px solid #aaa;
  }

  .check {
    border: 2px solid rgba(100, 100, 100, .2);
    background: var(--color-tint);

  }
</style>
