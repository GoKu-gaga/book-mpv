<template>
  <div class="container">
    <comment-list v-if="userinfo.openId" type="user" :comments="comments"></comment-list>
    <div v-if="userinfo.openId">
      <div class="page-title">我的图书</div>
      <card v-for="book in books" :key="book.id" :book="book"></card>
      <div v-if="!books.length">暂时没有添加图书</div>
    </div>
  </div>
</template>
<script>
import CommentList from '@/components/CommentList'
import Card from '@/components/Card'
import {get} from '@/util'
export default {
  components: {
    CommentList,
    Card
  },
  data () {
    return {
      comments: [],
      books: [],
      userinfo: {}
    }
  },
  methods: {
    init () {
      wx.showNavigationBarLoading()
      this.getComments()
      this.getBooks()
      wx.hideNavigationBarLoading()
    },
    async getBooks () {
      const books = await get('/weapp/booklist', {
        openid: this.userinfo.openId
      })
      this.books = books.list
    },
    async getComments () {
      const comments = await get('/weapp/commentlist', {openid: this.userinfo.openId})
      this.comments = comments.list
    }
  },
  onPullDownRefresh () {
    this.init()
    wx.stopPullDownRefresh()
  },
  onShow () {
    if (!this.userinfo.openId) {
      let userinfo = wx.getStorageSync('userinfo')
      if (userinfo) {
        this.userinfo = userinfo
        this.init()
      }
    }
  }
}
</script>
<style>


</style>
