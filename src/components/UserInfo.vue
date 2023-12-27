<template>
  <div class="header">
    <img :src="userInfo.imgUrl" alt="" class="avator" />
    <span>{{ userInfo.name }}</span>
  </div>
</template>

<script>
export default {
  props: {
    userInfo: {
      type: Object,
      default: () => {},
    },
  },
};
</script>

<style lang="scss" scoped>
.header {
  width: 100%;
  height: 100%;
  display: flex;
  align-items: center;

  .avator {
    width: 24px;
    height: 24px;
    border-radius: 50%;
    margin-right: 10px;
  }
}
</style>