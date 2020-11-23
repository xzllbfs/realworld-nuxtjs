<template>
  <div class="profile-page">

    <div class="user-info">
      <div class="container">
        <div class="row">
          <div class="col-xs-12 col-md-10 offset-md-1">
            <img :src="profile.image" class="user-img" />
            <h4>{{ profile.username }}</h4>
            <p>
              {{ profile.bio }}
            </p>
            <button
              class="btn btn-sm btn-outline-secondary action-btn"
              :class="{
                active: profile.following
              }"
              @click="onFollow(profile)"
              :disabled="profile.followDisabled"
            >
              <i class="ion-plus-round"></i>
              &nbsp;
              {{ profile.following ? 'Unfollow' : 'Follow' }} {{ profile.username }}
            </button>
          </div>

        </div>
      </div>
    </div>

    <div class="container">
      <div class="row">

        <div class="col-xs-12 col-md-10 offset-md-1">
          <div class="articles-toggle">
            <ul class="nav nav-pills outline-active">
              <li class="nav-item">
                <nuxt-link
                  class="nav-link"
                  :class="{
                    active: tab === 'my_articles'
                  }"
                  exact
                  :to="{
                    name: 'profile-username',
                    params: {
                      username: profile.username
                    }
                  }"
                >我的文章</nuxt-link>
              </li>
              <li class="nav-item">
                <nuxt-link
                  class="nav-link"
                  :class="{
                    active: tab === 'favorited_articles'
                  }"
                  exact
                  :to="{
                    name: 'profile-username',
                    params: {
                      username: profile.username
                    },
                    query: {
                      tab: 'favorited_articles'
                    }
                  }"
                >我喜欢的文章</nuxt-link>
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
                    name: 'profile-username',
                    params: {
                      username: profile.username
                    },
                    query: {
                      page: item,
                      tab: tab
                    }
                  }"
                >{{ item }}</nuxt-link>
              </li>
            </ul>
          </nav>
        </div>

      </div>
    </div>

  </div>
</template>

<script>
import {
  followUser,
  unfollowUser,
  getUserProfiles
} from '@/api/user'
import {
  getArticles,
  getYourFeedArticles
} from '@/api/article'
import articlesPreview from '../../components/articles-preview.vue'

export default {
  components: { articlesPreview },
  middleware: 'authenticated',
  name: 'profile-username',
  async asyncData ({ params, query }) {
    const page = Number.parseInt(query.page|| 1)
    const limit = 20
    const tab = query.tab || 'my_articles'

    const loadArticles = tab === 'my_articles'
      ? getArticles
      : getYourFeedArticles
    const [ articleRes, profileRes ] = await Promise.all([
      loadArticles({
        limit,
        offset: (page - 1) * limit,
        author: params.username
      }),
      getUserProfiles(params.username)
    ])
    const { articles, articlesCount } = articleRes.data
    const { profile } = profileRes.data
    return {
      articles,
      articlesCount,
      profile,
      page,
      limit,
      tab
    }
  },
  watchQuery: ['page', 'tag', 'tab'],
  computed: {
    totalPage () {
      return Math.ceil(this.articlesCount / this.limit)
    }
  },
  methods: {
    async onFollow (profile) {
      profile.followDisabled = true
      if (profile.following) {
        // 取消关注
        await unfollowUser(profile.username)
        profile.following = false
      } else {
        // 添加关注
        await followUser(profile.username)
        profile.following = true
      }
      profile.followDisabled = false
    }
  }
}
</script>

<style>

</style>
