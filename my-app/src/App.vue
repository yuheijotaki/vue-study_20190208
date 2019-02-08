<template>
  <div id="app">
    <ul>
      <li v-for="post in posts" :key="post.title.rendered">
        <a :href="post.link" v-html="post.title.rendered"></a>
        <!-- <pre v-html="post.content.rendered"></pre> -->
      </li>
    </ul>
    <button
      class="button"
      :class="[{
        'is-loading': loading,
        'is-disabled': disabled
      }]"
      :disabled="disabled"
      @click="load"
    >次の記事を読み込む</button>
  </div>
</template>

<script>
// normalize.css を読み込む
import "normalize.css";
// Ajax通信ライブラリ
import axios from "axios";

export default {
  name: "App",
  data() {
    return {
      posts: [],
      page: 0,
      loading: false,
      disabled: false,
    }
  },
  mounted() {
    this.page = 1;
  },
  watch: {
    page() {
      const url = `https://blog.yuheijotaki.com/wp-json/wp/v2/posts?categories=2&per_page=20&page=${this.page}`;
      (async () => {
        try {
          const res = await axios.get(url);
          this.posts = this.posts.concat(res.data);
          this.loading = false;
        } catch (error) {
          console.log(error);
          this.empty();
        }
      })();
    }
  },
  methods: {
    load() {
      this.loading = true;
      this.page++;
    },
    empty() {
      this.loading = false;
      this.disabled = true;
    }
  },
};
</script>

<style lang="scss" scoped>
html,* {
  margin: 0;
  padding: 0
}
#app {
  max-width: 800px;
  margin: 40px auto;
  ul {
    padding-left: 1.4em;
    li {
      margin-bottom: 5px;
      font-size: 14px;
      line-height: 1.2;
      &:last-child {
        margin-bottom: 0;
      }
      a {
      }
      pre {
        font-size: 10px;
      }
    }
  }
  button {
    padding: 6px 12px;
    margin-top: 20px;
    font-size: 13px;
    line-height: 1.2;
    &.is-loading {
      background: red;
    }
    &.is-disabled {
      color: #aaa;
      background: #ccc;
    }
  }
}
</style>
