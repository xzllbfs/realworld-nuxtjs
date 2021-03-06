<template>
  <div class="home-page">

    <div class="banner">
      <div class="container">
        <h1 class="logo-font">Conduit</h1>
        <p>A place to share your knowledge.</p>
      </div>
    </div>

    <div class="container page">
      <div class="row">

        <div class="col-md-9">
          <div class="feed-toggle">
            <ul class="nav nav-pills outline-active">
              <li v-if="user" class="nav-item">
                <nuxt-link
                  class="nav-link"
                  :class="{
                    active: tab === 'your_feed'
                  }"
                  exact
                  :to="{
                    name: 'index',
                    query: {
                      tab: 'your_feed'
                    }
                  }"
                >你关注的</nuxt-link>
              </li>
              <li class="nav-item">
                <nuxt-link
                  class="nav-link"
                  :class="{
                    active: tab === 'global_feed'
                  }"
                  exact
                  :to="{
                    name: 'index'
                  }"
                >全部文章</nuxt-link>
              </li>
              <li v-if="tag" class="nav-item">
                <nuxt-link
                  class="nav-link"
                  :class="{
                    active: tab === 'tag'
                  }"
                  exact
                  :to="{
                    name: 'index',
                    query: {
                      tab: 'tag',
                      tag: tag
                    }
                  }"
                ># {{ tag }}</nuxt-link>
              </li>
            </ul>
          </div>

          <articles-preview
            :articles="articles"
            :articlesCount="articlesCount"
            :limit="limit"
            :page="page"
            :tab="tab"
          ></articles-preview>

          <nav>
            <ul class="pagination">
              <li
                class="page-item"
                :class="{
                  active: item === page
                }"
                v-for="item in totalPage"
                :key="item"
              >
                <nuxt-link
                  class="page-link"
                  :to="{
                    name: 'index',
                    query: {
                      page: item,
                      tag: $route.query.tag,
                      tab: tab
                    }
                  }"
                >{{ item }}</nuxt-link>
              </li>
            </ul>
          </nav>

        </div>

        <div class="col-md-3">
          <div class="sidebar">
            <p>标签列表</p>

            <div class="tag-list">
              <nuxt-link
                :to="{
                  name: 'index',
                  query: {
                    tab: 'tag',
                    tag: item
                  }
                }"
                class="tag-pill tag-default"
                v-for="item in tags"
                :key="item"
              >{{ item }}</nuxt-link>
            </div>
          </div>
        </div>

      </div>
    </div>

  </div>
</template>

<script>
import {
  getArticles,
  getYourFeedArticles
} from '@/api/article'
import { getTags } from '@/api/tag'
import { mapState } from 'vuex'
import articlesPreview from '../components/articles-preview.vue'

export default {
  components: { articlesPreview },
  name: 'HomeIndex',
  layout: 'default',
  async asyncData ({ query }) {
    const page = Number.parseInt(query.page|| 1)
    const limit = 10
    const tab = query.tab || 'global_feed'
    const tag = query.tag

    const loadArticles = tab === 'global_feed'
      ? getArticles
      : getYourFeedArticles
    const [ articleRes, tagRes ] = await Promise.all([
      loadArticles({
        limit,
        offset: (page - 1) * limit,
        tag
      }),
      getTags()
    ])

    const { articles, articlesCount } = articleRes.data
    const { tags } = tagRes.data

    articles.forEach(article => article.favoriteDisabled = false)
    return {
      articles, // 文章列表
      articlesCount, // 文章总数
      tags, // 标签列表
      limit, // 每页大小
      page, // 页码
      tab, // 选项卡
      tag // 数据标签
    }
  },
  // 路径中的query参数改变，触发asyncData
  watchQuery: ['page', 'tag', 'tab'],
  computed: {
    ...mapState(['user']),
    totalPage () {
      return Math.ceil(this.articlesCount / this.limit)
    }
  }
}
</script>

<style>

</style>
