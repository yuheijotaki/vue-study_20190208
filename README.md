# Vue.js / JSON から情報を引っ張ってくる その 2

## やること

- WordPress の記事一覧を WP REST API を用いてエンドポイントを作成
- Vue.js で記事タイトルの一覧を表示させる

## セットアップ

Vue CLI を使用。[前回](https://yuheijotaki.hatenablog.com/entry/2018/12/28/025438)と同じ工程。

## エンドポイント

こちらも前回と同様に WordPress の `wp-json` を使用します。

#### REST API の有効化

投稿にフィルターをかけてエンドポイントを作れるように、[WordPress REST API](https://wordpress.org/plugins/rest-api/) プラグインをインストールします。

特定のカテゴリーの一覧記事を取得する場合、 `filter[]=` は以前のバージョンの仕様ということなのっで、[REST API Handbook](https://developer.wordpress.org/rest-api/reference/) を眺めつつ下記も参考にしました。  
[WP REST API でブログからお知らせカテゴリ記事を取得してみる【WordPress】【Python】 \| Cosnomi Blog](https://blog.cosnomi.com/archives/1068)

#### エンドポイントの JSON URL

https://blog.yuheijotaki.com/ の `Develop` カテゴリー一覧記事は、 `https://blog.yuheijotaki.com/wp-json/wp/v2/posts?categories=2` となります。  
これに `per_page` で表示件数を変更したり、いろいろできる。  
参考：[REST API Handbook | Posts | Arguments](https://developer.wordpress.org/rest-api/reference/posts/#arguments)

## Vue.js 側の処理

下記にやってみたいことがだいたいありました。  
[《WordPress》2017 年末に WP REST API で取得して Vue\.js で描画するまでのまとめ。 \- Qiita](https://qiita.com/uto-usui/items/4eb21aec704b888936d0)

#### App.vue

一部省略

```html
<template>
  <div id="app">
    <ul>
      <li v-for="post in posts" :key="post.title.rendered">
        <a :href="post.link" v-html="post.title.rendered"></a>
        <!-- <pre v-html="post.content.rendered"></pre> -->
      </li>
    </ul>
    <button>次の記事を読み込む</button>
  </div>
</template>

<script>
  export default {
    name: "App",
    data() {
      return {
        posts: [],
        page: 0,
        loading: false,
        disabled: false
      };
    },
    mounted() {
      this.page = 1;
    },
    watch: {
      page() {
        const url = `https://blog.yuheijotaki.com/wp-json/wp/v2/posts?categories=2&page=${
          this.page
        }`;
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
    }
  };
</script>
```

## まとめ

[**GitHub**](https://github.com/yuheijotaki/vue-study_20190208)

- .vue ファイルの編集は Qiita の参考記事を元に少しいじった程度なのでスクラッチで自分が書くのは難しい。。
- なので Vue.js より REST API のほうが詰まった。ただ `const res = await axios.get(url);` あたりは定型文的なものなのかなと思いました。
- 次回はカテゴリー一覧出力してクリックしたら記事一覧を表示をやる。
