<template>
  <div>
    <router-view></router-view>
    <h1 style="text-align:center;">Network topology visualization</h1>
    <div class="container">
      <router-link to="/" class="button">Home</router-link>
      <router-link to="/about" class="button">Algorithm display</router-link>
    </div>
    <!-- <router-link to="/">Algorithm display</router-link> -->
    <!-- <router-link to="/about">About</router-link> -->
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
  /* width: 32vh; */
  background-color: green; /* 设置背景颜色 */
}

.button {
  color: white;
  padding: 10px;
  flex-grow: 1;
  text-align: center;
}
</style>