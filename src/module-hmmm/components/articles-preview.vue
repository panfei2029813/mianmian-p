<template>
  <div class="container">
    <el-dialog
      title="提示"
      :visible.sync="isShowPages"
      width="50%"
      @close="onIsArticlePageShow"
    >
      <h3>{{ articlesMsg.title }}</h3>
      <div class="header">
        <span>{{ articlesMsg.createTime | parseTimeByString() }}</span>
        <span>超级管理员</span>
        <span class="el-icon-view"></span>
        <span>{{ articlesMsg.visits }}</span>
      </div>
      <div v-html="articlesMsg.articleBody"></div>
      <span slot="footer" class="dialog-footer">
        <el-button @click="isShowPages = false">取 消</el-button>
        <el-button type="primary" @click="isShowPages = false">确 定</el-button>
      </span>
    </el-dialog>
  </div>
</template>

<script>
import { detail } from '@/api/hmmm/articles'
import { parseTimeByString, html2Text } from '@/filters'
export default {
  props: {
    isArticlePageShow: {
      type: Boolean
    },
    thisRow: {
      type: [Object]
    }
  },
  data() {
    return {
      isShowPages: false,
      articlesMsg: {}
    }
  },
  created() {
    console.log(321)
  },
  mounted() {},
  methods: {
    async loadArticleMEssage() {
      try {
        const { data } = await detail(this.thisRow)
        console.log(data, 456)
        this.articlesMsg = data
      } catch (error) {}
    },
    onIsArticlePageShow() {
      this.$emit('update:isArticlePageShow', false)
    }
  },
  computed: {},
  watch: {
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
