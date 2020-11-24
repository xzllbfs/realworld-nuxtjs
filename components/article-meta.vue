<template>
  <div class="article-meta">
    <nuxt-link :to="{
      name: 'profile-username',
      params: {
        username: article.author.username
      }
    }">
      <img :src="article.author.image" />
    </nuxt-link>
    <div class="info">
      <nuxt-link class="author" :to="{
        name: 'profile-username',
        params: {
          username: article.author.username
        }
      }">
        {{ article.author.username }}
      </nuxt-link>
      <span class="date">{{ article.createdAt | date('MMMM DD, YYYY') }}</span>
    </div>
    <nuxt-link
      class="btn btn-outline-secondary btn-sm"
      v-if="article.author.username === user.username"
      :to="{
        name: 'editor-slug',
        params: {
          slug: article.slug
        }
      }"
    >
      <i class="ion-edit"></i>
      编辑文章
    </nuxt-link>
    <button
      v-else
      class="btn btn-sm btn-outline-secondary"
      :class="{
        active: article.author.following
      }"
      @click="onFollow(article.author)"
      :disabled="article.author.followDisabled"
    >
      <i class="ion-plus-round"></i>
      &nbsp;
      {{ article.author.following ? '取消关注' : '关注' }}
    </button>
    &nbsp;&nbsp;
    <button
      class="btn btn-sm btn-outline-primary"
      :class="{
        active: article.favorited
      }"
      @click="onFavorite(article)"
      :disabled="article.favoriteDisabled"
    >
      <i class="ion-heart"></i>
      &nbsp;
      点赞 <span class="counter">({{ article.favoritesCount }})</span>
    </button>
  </div>
</template>

<script>
import {
  addFavorite,
  deleteFavorite
} from '@/api/article'
import {
  followUser,
  unfollowUser
} from '@/api/user'
import { mapState } from 'vuex'

export default {
  name: 'ArticleMeta',
  props: {
    article: {
      type: Object,
      required: true
    }
  },
  computed: {
    ...mapState(['user']),
  },
  methods: {
    async onFollow (author) {
      author.followDisabled = true
      if (author.following) {
        // 取消关注
        await unfollowUser(author.username)
        author.following = false
      } else {
        // 添加关注
        await followUser(author.username)
        author.following = true
      }
      author.followDisabled = false
    },
    async onFavorite (article) {
      article.favoriteDisabled = true
      if (article.favorited) {
        // 取消点赞
        await deleteFavorite(article.slug)
        article.favorited = false
        article.favoritesCount += -1
      } else {
        // 添加点赞
        await addFavorite(article.slug)
        article.favorited = true
        article.favoritesCount += 1
      }
      article.favoriteDisabled = false
    }
  }
}
</script>

<style>

</style>
