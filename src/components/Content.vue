<template>
  <transition name="slide-right">
    <div class="content" v-if="bookAvailable">
      <div class="content-wrapper">
        <h5>Content</h5>
        <div class="content-item" v-for="(item, index) in navigation.toc" :key="index" @click="jumpTo(item.href)"></div>
        <div class="text"> {{ item.label }}</div>
      </div>
    </div>
    <div class="empty" v-else>loading...</div>
  </transition>
</template>
<script>
  export default {
    props: {
      ifShowContent: Boolean,
      navigation: Object,
      bookAvailable: Boolean
    },
    methods: {
      jumpTo (target) {
        this.$emit('jumpTo', target)
      }
    }
  }
</script>
<style lang="scss" scoped>
  @import '@/assets/styles/global.scss';
  .content {
    .content-wrapper {
      position: absolute;
      top: 0;
      left: 0;
      width: 100%;
      height: 100%;
      z-index: 101;
      display: flex;
      flex-direction: column;
      background: white;
      h5 {
        @include center;
        padding-top: px2rem(10);
        font-size: px2rem(20);
      }
      .content-item {
        flex: 1;
        width: 100%;
        height: px2rem(10);
        line-height: px2rem(10);
        padding-left: px2rem(15);
      }
      .text {
        width: 100%;
        color: #333;
        cursor: pointer;
        font-size: px2rem(10);
      }
    }
    .empty {}
  }
</style>
