<template>
  <div id="app">
    <ul>
      <li>name: {{results.name}}</li>
      <li>home: {{results.home}}</li>
      <li>timezone_string: {{results.timezone_string}}</li>
    </ul>
  </div>
</template>

<script>
// normalize.css を読み込む
import "normalize.css";
// Ajax通信ライブラリ
import axios from "axios";
// JSON の URL
const jsonUrl = "https://blog.yuheijotaki.com/wp-json/";

export default {
  name: "App",
  data() {
    return {
      results: []
    };
  },
  mounted() {
    // JSON取得
    axios.get(jsonUrl).then(response => {
      this.results = response.data;
    });
  }
};
</script>

<style>
</style>
