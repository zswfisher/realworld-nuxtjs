<template>
  <div class="article-page">
    <div class="banner">
      <div class="container">
        <h1>{{ article.title }}</h1>
        <ArticleMeta :article="article" :isAuthor="isAuthor" @deleteArticle="deleteArticle" />
      </div>
    </div>

    <div class="container page">
      <div class="row article-content">
        <div class="col-md-12" v-html="article.body"></div>
      </div>

      <hr />

      <div class="article-actions">
        <ArticleMeta :article="article" :isAuthor="isAuthor" @deleteArticle="deleteArticle" />
      </div>

      <div class="row">
        <div class="col-xs-12 col-md-8 offset-md-2">
          <ArticleComments :article="article"/>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { getArticle, deleteArticle } from '@/api/article'
import MarkdownIt from 'markdown-it'
import ArticleMeta from './components/article-meta'
import ArticleComments from './components/article-comments'
import { mapState } from 'vuex'

export default {
  components: { 
    ArticleMeta, 
    ArticleComments 
  },
  name: 'Article',
  computed: {
    ...mapState(['user']),
    isAuthor() {
      return this.user.username === this.article.author.username
    }
  },
  async asyncData({ params }) {
    const { data } = await getArticle(params.slug)
    console.log('eee', data)
    const { article } = data
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
        {
          hid: 'description',
          name: 'description',
          content: this.article.description
        }
      ]
    }
  },
  methods: {
    async deleteArticle() {
      const resp = await deleteArticle(this.article.slug);
      
    }
  }
};
</script>

<style>
</style>