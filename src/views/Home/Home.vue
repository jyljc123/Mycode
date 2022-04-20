<template>
  <div class="home-container">
    <van-nav-bar title="黑马头条" :fixed="true" />
    <van-pull-refresh v-model="refreshing" @refresh="onRefresh">
      <van-list
        v-model="loading"
        :finished="finished"
        finished-text="没有更多了"
        @load="onLoad"
      >
        <ArticleInfo
          v-for="item in articleList"
          :key="item.id"
          :title="item.title"
          :author="item.aut_name"
          :comment="item.comm_count"
          :date="item.pubdate"
          :cover="item.cover"
        ></ArticleInfo>
      </van-list>
    </van-pull-refresh>
  </div>
</template>

<script>
// 导入需要的组件
import ArticleInfo from '@/components/article/ArticleInfo'
import { getArticleListAPI } from '@/api/articleAPI'
export default {
  name: 'Home',
  data() {
    return {
      // 页码值
      page: 1,
      // 每页显示多少条数据
      limit: 10,
      // 文章列表
      articleList: [],
      loading: true,
      finished: false,
      refreshing: false
    }
  },
  created() {
    this.initArtcleList()
  },
  methods: {
    async initArtcleList(isOnFlash) {
      const { data: res } = await getArticleListAPI(this.page, this.limit)
      // 发起get请求获取文章的列表数据
      if (isOnFlash) {
        this.articleList = [...res, ...this.articleList]
        this.refreshing = false
      } else {
        this.articleList = [...this.articleList, ...res]
        this.loading = false
      }
      if (res.length === 0) {
        this.finished = true
      }
    },
    onLoad() {
      console.log('触发了onload事件')
      this.page++
      this.initArtcleList()
    },
    onRefresh() {
      console.log('触发了下拉刷新')
      this.page++
      this.initArtcleList(true)
    }
  },
  components: {
    ArticleInfo
  }
}
</script>

<style lang="less">
.home-container {
  padding: 46px 0 50px 0;
  .van-nav-bar {
    background-color: #007bff;
  }
  .van-nav-bar__title {
    color: white;
  }
}
</style>
