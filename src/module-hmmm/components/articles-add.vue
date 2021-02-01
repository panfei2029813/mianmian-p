<template>
  <div class="container">
    <el-dialog
      title="提示"
      :visible.sync="isShowPages"
      width="50%"
      @close="onIsArticleAddShow"
    >
      <el-form
        :model="numberValidateForm"
        ref="numberValidateForm"
        label-width="100px"
        class="demo-ruleForm"
      >
        <el-form-item label="文章标题" prop="title" :rules="formRules.title">
          <el-input
            v-model.number="numberValidateForm.title"
            autocomplete="off"
          ></el-input>
        </el-form-item>

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

        <el-form-item label="视屏链接" prop="videoURL">
          <el-input
            v-model.number="numberValidateForm.videoURL"
            autocomplete="off"
          ></el-input>
        </el-form-item>

        <el-form-item>
          <el-button type="primary" @click="submitForm(numberValidateForm)"
            >提交</el-button
          >
          <el-button @click="resetForm('numberValidateForm')">重置</el-button>
        </el-form-item>
      </el-form>
    </el-dialog>
  </div>
</template>

<script>
import { quillEditor } from 'vue-quill-editor' // 调用编辑器
import 'quill/dist/quill.core.css'
import 'quill/dist/quill.snow.css'
import 'quill/dist/quill.bubble.css'
import { add, update } from '@/api/hmmm/articles'

export default {
  props: {
    isArticleAddShow: {
      type: Boolean
    },
    edit: {
      type: Boolean
    },
    articls: {
      type: Object,
      required: true
    }
  },
  components: {
    quillEditor
  },
  data() {
    return {
      isShowPages: false,
      articlesMsg: {},
      formRules: {
        title: [{ required: true, message: '请输入标题' }],
        articleBody: [{ required: true, message: '请输入内容' }]
      },
      numberValidateForm: {
        title: '',
        articleBody: '',
        videoURL: ''
      }
    }
  },
  created() {
    console.log(321)
  },
  mounted() {},
  methods: {
    onIsArticleAddShow() {
      this.$emit('update:isArticleAddShow', false)
    },
    async submitForm(formName) {
      try {
        if (this.edit) {
          await update(this.articls)
          this.$emit('update:edit', false)
        }
        await add(formName)

        this.isShowPages = false
        this.resetForm('numberValidateForm')
      } catch (error) {
        console.log(error)
      }
    },
    resetForm(formName) {
      this.$refs[formName].resetFields()
    }
  },
  computed: {},
  watch: {
    isArticleAddShow() {
      this.isShowPages = this.isArticleAddShow
      this.numberValidateForm.title = this.articls.title
      this.numberValidateForm.articleBody = this.articls.articleBody
      this.numberValidateForm.videoURL = this.articls.videoURL
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
