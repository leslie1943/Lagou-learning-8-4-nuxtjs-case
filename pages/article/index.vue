<template>
  <div class="article-page">
    <div class="banner">
      <div class="container">
        <h1>{{article.title}}</h1>

        <article-meta :article="article" />
      </div>
    </div>

    <div class="container page">
      <div class="row article-content">
        <div class="col-md-12">
          <p v-html="article.body"></p>
        </div>
      </div>

      <hr />

      <div class="article-actions">
        <article-meta :article="article" />
      </div>

      <div class="row">
        <div class="col-xs-12 col-md-8 offset-md-2">
          <article-comments :article="article" />
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { getArticle } from '@/api/article'
import MarkdownIt from 'markdown-it'
import ArticleMeta from './components/article-meta'
import ArticleComments from './components/article-comments'
export default {
  name: 'ArticleIndex',
  // 在路由匹配组件渲染之前会先执行中间件处理
  middleware: ['authenticated'],
  components: { ArticleMeta, ArticleComments },
  async asyncData({ query, params }) {
    const slug = params.slug
    const { data } = await getArticle(slug)
    const { article } = data
    // 添加辅助属性
    article.favoriteDisable = false
    article.followingDisable = false
    // slug:markdown-ddof1g 可以测试 markdown 是否生效
    const md = new MarkdownIt()
    article.body = md.render(article.body)
    return {
      article
    }
  },
  head() {
    return {
      title: `${this.article.title} - RealWorld`,
      meta: [
        { hid: 'description', name: 'description', content: this.article.description },
      ]
    }
  }
}
</script>

<style>
</style>