<template>
  <div>
    <div>
    <img src="./校园网络拓扑.jfif" alt="封面图片" style="width: 90%; height: auto;">
    <h1 style="text-align:center;">Network topology visualization</h1>
    </div>
    <div class="container">
      <router-link to="/" class="button">Algorithm display</router-link>
      <router-link to="/about" class="button">About</router-link>
    </div>
    <!-- <router-link to="/">Algorithm display</router-link> -->
    <!-- <router-link to="/about">About</router-link> -->
    <router-view></router-view>
  </div>
</template>

<script>
import { defineComponent } from 'vue';
import { RouterLink, RouterView } from 'vue-router';

export default defineComponent({
  components: {
    RouterLink,
    RouterView,
  },
});
</script>

<style>
.container {
  display: flex;
  justify-content: center; /* 在水平方向上居中 */
  align-items: center; /* 在垂直方向上居中 */
  height: 4vh; /* 设置容器高度为视口高度 */
  width: 32vh;
  background-color: green; /* 设置背景颜色 */
}

.button {
  color: white;
  padding: 10px;
  flex-grow: 1;
  text-align: center;
}
</style>