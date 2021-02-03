<template>
  <div class="container">
    <el-dialog title="题目预览" :visible.sync="dialogVisible" width="70%">
      <el-row :gutter="20">
        <el-col :span="6">
          <span
            >【题型】:
            <span v-if="questionsDetail.questionType == 1">单选</span>
            <span v-else-if="questionsDetail.questionType == 2">多选</span>
            <span v-else>简答</span>
            题</span
          >
        </el-col>
        <el-col :span="6"
          ><span>【编号】: {{ questionsDetail.id }}</span></el-col
        >
        <el-col :span="6">
          <span
            >【难度】:
            <span v-if="questionsDetail.difficulty == 1">简单</span>
            <span v-else-if="questionsDetail.difficulty == 2">一般</span>
            <span v-else>困难</span>
          </span>
        </el-col>
        <el-col :span="6"
          ><span>【标签】: {{ questionsDetail.tags }}</span></el-col
        >
      </el-row>
      <el-row :gutter="20">
        <el-col :span="6"
          ><span>【学科】: {{ questionsDetail.subjectName }}</span></el-col
        >
        <el-col :span="6"
          ><span>【目录】: {{ questionsDetail.directoryName }}</span></el-col
        >
        <el-col :span="6"
          ><span>【方向】: {{ questionsDetail.direction }}</span></el-col
        >
        <el-col :span="6"><span></span></el-col>
      </el-row>
      <el-divider></el-divider>
      <p>【题干】</p>
      <p>
        <span v-html="questionsDetail.question"></span>
      </p>
      <p>
        <span v-if="questionsDetail.questionType == 1"
          >单选题选项:(一下选中的选项为正确选项)</span
        >
        <span v-else-if="questionsDetail.questionType == 2"
          >多选题选项:(一下选中的选项为正确选项)</span
        >
        <span v-else></span>
      </p>
      <!-- 单选 -->
      <div v-if="questionsDetail.questionType == 1">
        <el-radio
          v-for="item in questionsDetail.options"
          :key="item.id"
          :value="isRight"
          :label="item.isRight"
          style="width: 100%; margintop: 20px"
        >
          {{ item.code }}. {{ item.title }}
        </el-radio>
      </div>
      <!-- 多选 -->
      <div v-if="questionsDetail.questionType == 2">
        <el-checkbox-group :value="checkList">
          <el-checkbox
            v-for="item in questionsDetail.options"
            :key="item.id"
            :value="isRight"
            :label="item.isRight"
            style="width: 100%; margintop: 20px"
          >
            {{ item.code }}. {{ item.title }}
          </el-checkbox>
        </el-checkbox-group>
      </div>
      <el-divider></el-divider>
      <span
        >【参考答案】:
        <el-button type="danger" @click="openVideo">视频答案预览</el-button>
      </span>
      <p>
        <video
          v-if="videoFlag"
          controls="controls"
          style="background-color: rgb(0, 0, 0);
            width: 100%;
            height: 100%;
            display: block;"
          :src="questionsDetail.videoURL"
        ></video>
      </p>
      <el-divider></el-divider>
      <div>
        <span>【答案解析】:</span>
        <span v-html="questionsDetail.answer"></span>
      </div>
      <el-divider></el-divider>
      <span> 【题目备注】:{{ questionsDetail.remarks }} </span>
      <span slot="footer" class="dialog-footer">
        <el-button @click="dialogVisible = false">取 消</el-button>
        <el-button type="primary" @click="dialogVisible = false"
          >确 定</el-button
        >
      </span>
    </el-dialog>
  </div>
</template>
<script>
import { detail } from '@/api/hmmm/questions'
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
      dialogVisible: false,
      questionsDetail: [],
      videoFlag: false,
      isRight: 1,
      checkList: [1]
    }
  },
  created() {},
  methods: {
    async getRandoms() {
      try {
        const { data } = await detail(this.previewMsg)
        this.questionsDetail = data
        console.log(this.questionsDetail)
        console.log(123)
      } catch (err) {
        console.log(err)
      }
    },
    openVideo() {
      this.videoFlag = true
    }
  },
  watch: {
    previewDig() {
      this.dialogVisible = this.previewDig
      this.getRandoms()
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
