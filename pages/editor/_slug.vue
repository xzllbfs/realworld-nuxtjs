<template>
  <div class="editor-page">
    <div class="container page">
      <div class="row">
        <div class="col-md-10 offset-md-1 col-xs-12">
          <ul class="error-messages">
            <template v-for="(messages, field) in errors">
              <li
                v-for="(message, index) in messages"
                :key="index"
              >{{ field }} {{ message }}</li>
            </template>
          </ul>
          <fieldset>
            <fieldset class="form-group">
              <input v-model="article.title" type="text" class="form-control form-control-lg" placeholder="文章标题">
            </fieldset>
            <fieldset class="form-group">
              <input v-model="article.description" type="text" class="form-control" placeholder="这篇文章是关于什么的？">
            </fieldset>
            <fieldset class="form-group">
              <textarea v-model="article.body" class="form-control" rows="8" placeholder="写下你的文章 (用 markdown 格式)"></textarea>
            </fieldset>
            <fieldset class="form-group">
              <input v-model="tag" type="text"
                @keydown.enter="onEnter(tag)"
                class="form-control" placeholder="输入标签名称，按回车键添加至列表">
              <div class="tag-list">
                标签列表：
                <span
                  class="tag-default tag-pill ng-binding ng-scope"
                  v-for="(item, index) in article.tagList"
                  :key="index"
                >
                  <i class="ion-close-round" @click="delTag(index)"></i>
                  {{ item }}
                </span>
              </div>
            </fieldset>
            <button
              @click="onSubmit"
              class="btn btn-lg pull-xs-right btn-primary"
              type="button"
            >
              发布文章
            </button>
          </fieldset>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { createArticle, updateArticle, getArticle } from '@/api/article'
import MarkdownIt from 'markdown-it'

export default {
  // 在路由匹配组件渲染之前会先执行中间件处理
  middleware: 'authenticated',
  name: 'EditorIndex',
  async asyncData ({ params }) {
    if (!params.slug) return
    const { data } = await getArticle(params.slug)
    const { article } = data
    // console.log(article.body)
    // const md = new MarkdownIt()
    // article.body = md.render(article.body)
    return {
      article
    }
  },
  data() {
    return {
      article: {
        title: '',
        description: '',
        body: '',
        tagList: []
      },
      tag: '',
      errors: {}
    }
  },
  methods: {
    async onSubmit () {
      try {
        const paramSlug = this.$route.params.slug
        const { data } = paramSlug
        ? await updateArticle(paramSlug, this.article)
        : await createArticle({ article: this.article })

        this.$router.push(`/article/${data.article.slug}`)
      } catch (err) {
        this.errors = err.response.data.errors
      }
    },
    onEnter (tag) {
      this.article.tagList.push(tag)
      this.tag = ''
    },
    delTag (index) {
      this.article.tagList.splice(index, 1)
    }
  },
  mounted() {
    const article = this.$route.params.article
    if (article) this.article = article
  }
}
</script>

<style>

</style>
