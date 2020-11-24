<template>
  <div>
    <div class="card comment-form">
      <div class="card-block">
        <textarea v-model="comment.body" class="form-control" placeholder="写下评论..." rows="3"></textarea>
      </div>
      <div class="card-footer">
        <img :src="article.author.image" class="comment-author-img" />
        <button class="btn btn-sm btn-primary" @click="onAddComment">
        发表评论
        </button>
      </div>
    </div>
    <div
      class="card"
      v-for="comment in comments"
      :key="comment.id"
    >
      <div class="card-block">
        <p class="card-text">{{ comment.body }}</p>
      </div>
      <div class="card-footer">
        <nuxt-link class="comment-author" :to="{
          name: 'profile-username',
          params: {
            username: comment.author.username
          }
        }">
          <img :src="comment.author.image" class="comment-author-img" />
        </nuxt-link>
        &nbsp;
        <nuxt-link class="comment-author" :to="{
          name: 'profile-username',
          params: {
            username: comment.author.username
          }
        }">
          {{ comment.author.username }}
        </nuxt-link>
        <span class="date-posted">{{ comment.createdAt | date('MMM DD, YYYY') }}</span>
      </div>
    </div>
  </div>
</template>

<script>
import { getComments, addComment } from '@/api/article'
import { mapState } from 'vuex'

export default {
  name: 'ArticleComments',
  props: {
    article: {
      type: Object,
      required: true
    }
  },
  data () {
    return {
      comment: {
        body: '',
        author: {}
      },
      comments: [] // 文章列表
    }
  },
  computed: {
    ...mapState(['user'])
  },
  async mounted () {
    const { data } = await getComments(this.article.slug)
    this.comments = data.comments
  },
  methods: {
    async onAddComment () {
      const comment = {
        body: this.comment.body,
        author: this.user
      }
      const { data } = await addComment(this.article.slug, comment)
      this.comments.unshift(comment)

      this.comment.body = ''
    }
  }
}
</script>

<style>

</style>
