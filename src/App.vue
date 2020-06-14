<!--
 * @Descripttion: 描述
 * @Author: ljz
 * @Date: 2020-06-13 10:09:51
 * @LastEditors: ljz
 * @LastEditTime: 2020-06-13 13:01:22
--> 
<template>
  <div id="app">
    <my-header />
    <router-view />
  </div>
</template>

<script>
import Header from "./components/Header";
export default {
  name: "App",
  components: {
    "my-header": Header
  }
};
</script>

<style lang="less" scoped>
</style>
