<template>
  <div class="container">
    <el-dialog
      title="提示"
      :visible.sync="isShowPages"
      width="50%"
      @close="onIsArticlePageShow"
    >
      <!-- 标题 -->
      <h3>{{ articlesMsg.title }}</h3>
      <!-- 标题 -->
      <!-- 信息 -->
      <div class="header">
        <span>{{ articlesMsg.createTime | parseTimeByString() }}</span>
        <span>超级管理员</span>
        <span class="el-icon-view"></span>
        <span>{{ articlesMsg.visits }}</span>
      </div>
      <!-- 信息 -->
      <!-- 内容 -->
      <div v-html="articlesMsg.articleBody"></div>
      <!-- 内容 -->
      <!-- 底部 -->
      <span slot="footer" class="dialog-footer">
        <el-button @click="isShowPages = false">取 消</el-button>
        <el-button type="primary" @click="isShowPages = false">确 定</el-button>
      </span>
      <!-- 底部 -->
    </el-dialog>
  </div>
</template>

<script>
import { detail } from '@/api/hmmm/articles' // api
import { parseTimeByString } from '@/filters' // 时间格式化
export default {
  props: {
    // 会话框是否显示
    isArticlePageShow: {
      type: Boolean
    },
    // 当前行信息
    thisRow: {
      type: [Object]
    }
  },
  filters: {
    parseTimeByString
  },

  data() {
    return {
      // 会话框显示开关
      isShowPages: false,
      // 文章内容
      articlesMsg: {}
    }
  },

  methods: {
    // 获取文章详情
    async loadArticleMEssage() {
      try {
        const { data } = await detail(this.thisRow)
        console.log(data, 456)
        this.articlesMsg = data
      } catch (error) {}
    },

    // 文章详情会话框显示隐藏数据
    onIsArticlePageShow() {
      this.$emit('update:isArticlePageShow', false)
    }
  },

  watch: {
    // 监听会话框是否展示
    isArticlePageShow() {
      this.isShowPages = this.isArticlePageShow
      this.loadArticleMEssage()
    }
  }
}
</script>

<style scoped lang="less">
.header span {
  margin-right: 5px;
}
</style>
