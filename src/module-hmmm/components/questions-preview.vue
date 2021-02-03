<template>
  <div class="container">
    <el-dialog title="题目预览" :visible.sync="dialogVisible" width="60%">
      <!-- <div>
        <el-row style="margin-bottom:20px">
          <el-col :span="6">
            <div>
              【题型】：
              <span v-if="previewData.questionType == 1">简单</span>
              <span v-if="previewData.questionType == 2">多选</span>
              <span v-if="previewData.questionType == 3">单选</span>
            </div>
          </el-col>
          <el-col :span="6">
            <div>【编号】：{{ previewData.id }}</div>
          </el-col>
          <el-col :span="6">
            <div>
              【难度】：
              <span v-if="previewData.difficulty == 1">简单</span>
              <span v-if="previewData.difficulty == 2">一般</span>
              <span v-if="previewData.difficulty == 3">困难</span>
            </div>
          </el-col>
          <el-col :span="6">
            <div>【标签】：{{ previewData.tags }}</div>
          </el-col>
        </el-row>
        <el-row style="margin-bottom: 20px">
          <el-col :span="6">
            <div>【学科】：{{ previewData.subjectName }}</div>
          </el-col>
          <el-col :span="6">
            <div>【目录】：{{ previewData.directoryName }}</div>
          </el-col>
          <el-col :span="6">
            <div>【方向】：{{ previewData.direction }}</div>
          </el-col>
        </el-row>
        <hr />
        <div>
          【题干】：
          <p style="color:blue" v-html="previewData.question"></p>
          <div v-if="previewData.questionType == 3">
            <span>单选题选项：(一下选中的选项为正确答案)</span>
            <el-radio
              v-for="(item1, index) in previewData.options"
              :key="index"
              v-model="radio"
              :label="item1.isRight"
              >{{ item1.title }}</el-radio
            >
          </div>
          <div v-if="previewData.questionType == 2">
            <div>多选题选项：(一下选中的选项为确定答案)</div>
            <el-checkbox
              v-for="(item2, index) in previewData.options"
              :key="index"
            />
          </div>
        </div>
      </div> -->
    </el-dialog>
  </div>
</template>
<script>
import { randoms } from '@/api/hmmm/questions'
export default {
  props: {
    preview: {
      type: Object,
      require: true
    },
    previewDig: {
      type: Boolean,
      require: true
    },
    previewMsg: {
      type: Object,
      require: true
    }
  },
  data() {
    return {
      dialogVisible: false
    }
  },
  async getRandoms() {
    try {
      const { data } = await randoms(this.previewMsg)
      console.log(data)
    } catch (err) {
      console.log(err)
    }
  },
  watch: {
    previewDig() {
      this.dialogVisible = this.previewDig
    },
    dialogVisible() {
      if (!this.dialogVisible) {
        this.$emit('update:previewDig', false)
      }
    }
  }
}
</script>

<style scoped lang="less"></style>
