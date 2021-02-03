<template>
  <div class="container">
    <el-dialog
      title="提示"
      :visible.sync="isShowPages"
      width="50%"
      @close="onIsArticleAddShow"
    >
      <!-- form表单 -->
      <el-form
        :model="numberValidateForm"
        ref="numberValidateForm"
        label-width="100px"
        class="demo-ruleForm"
      >
        <!-- 文章标题 -->
        <el-form-item label="文章标题" prop="title" :rules="formRules.title">
          <el-input
            v-model.number="numberValidateForm.title"
            autocomplete="off"
          ></el-input>
        </el-form-item>
        <!-- 文章标题 -->
        <!-- 富文本编辑器 -->
        <el-form-item
          label="文章内容"
          prop="articleBody"
          :rules="formRules.articleBody"
        >
          <div class="edit_container">
            <quill-editor
              v-model="numberValidateForm.articleBody"
              ref="myQuillEditor"
            >
            </quill-editor>
          </div>
        </el-form-item>
        <!-- 富文本编辑器 -->
        <!-- 视频链接 -->
        <el-form-item label="视屏链接" prop="videoURL">
          <el-input
            v-model.number="numberValidateForm.videoURL"
            autocomplete="off"
          ></el-input>
        </el-form-item>
        <!-- 视频链接 -->
        <!-- 提交取消按钮 -->
        <el-form-item>
          <el-button type="primary" @click="submitForm(numberValidateForm)"
            >提交</el-button
          >
          <el-button @click="resetForm('numberValidateForm')">重置</el-button>
        </el-form-item>
        <!-- 提交取消按钮 -->
      </el-form>
      <!-- form表单 -->
    </el-dialog>
  </div>
</template>

<script>
import { quillEditor } from 'vue-quill-editor' // 调用编辑器
import 'quill/dist/quill.core.css'
import 'quill/dist/quill.snow.css'
import 'quill/dist/quill.bubble.css'
import { add, update } from '@/api/hmmm/articles' // api

export default {
  props: {
    // 会话框显示
    isArticleAddShow: {
      type: Boolean
    },
    // 编辑开关
    edit: {
      type: Boolean
    },
    // 文章内容
    articls: {
      type: Object,
      required: true
    }
  },

  components: {
    quillEditor // 富文本编辑器组件
  },

  data() {
    return {
      // 会话框显示?
      isShowPages: false,
      // 文章信息
      articlesMsg: {},
      // 表单验证规则
      formRules: {
        title: [{ required: true, message: '请输入标题' }],
        articleBody: [{ required: true, message: '请输入内容' }]
      },
      // 表单数据
      numberValidateForm: {
        title: '',
        articleBody: '',
        videoURL: ''
      }
    }
  },

  methods: {
    // 文章怎天显示父组件修改值
    onIsArticleAddShow() {
      this.$emit('update:isArticleAddShow', false)
    },

    // 提交表单数据
    async submitForm(formName) {
      try {
        // 判断事编辑还事增添状态
        if (this.edit) {
          await update(this.articls)
        } else {
          await add(formName)
        }

        this.$emit('update:edit', false)
        this.isShowPages = false
        this.resetForm('numberValidateForm') // 重置表单
      } catch (error) {
        console.log(error)
      }
    },

    // 重置表单
    resetForm(formName) {
      this.$refs[formName].resetFields()
    }
  },

  watch: {
    // 监听会话框状态
    isArticleAddShow() {
      this.isShowPages = this.isArticleAddShow
      // 判断是不是编辑状态
      if (this.edit) {
        // 编辑状态赋初始值
        console.log(this.edit)
        this.numberValidateForm.title = this.articls.title
        this.numberValidateForm.articleBody = this.articls.articleBody
        this.numberValidateForm.videoURL = this.articls.videoURL
      } else {
        // 增添状态不赋值
        this.numberValidateForm = {}
        console.log(this.edit)
      }
    }
  }
}
</script>

<style scoped lang="less">
.header span {
  margin-right: 5px;
}
.edit_container {
  /deep/.ql-editor {
    height: 300px;
  }
}
</style>
