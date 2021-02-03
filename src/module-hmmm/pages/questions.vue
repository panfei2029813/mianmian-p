<template>
  <div class="container">
    <!-- 试题预览对话框 -->
    <el-dialog title="题目预览" :visible.sync="dialogVisible" width="60%">
      <div>
        <el-row style="margin-bottom: 20px">
          <el-col :span="6">
            <div>
              【题型】：
              <span v-if="previewData.questionType == 1">简单</span>
              <span v-if="previewData.questionType == 2">多选</span>
              <span v-if="previewData.questionType == 3">单选</span>
            </div>
          </el-col>
          <el-col :span="6">
            <div>【编号】：{{previewData.id}}</div>
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
            <div>【标签】：{{previewData.tags}}</div>
          </el-col>
        </el-row>
        <el-row style="margin-bottom: 20px">
          <el-col :span="6">
            <div>【学科】：{{previewData.subjectName}}</div>
          </el-col>
          <el-col :span="6">
            <div>【目录】：{{previewData.directoryName}}</div>
          </el-col>
          <el-col :span="6">
            <div>【方向】：{{previewData.direction}}</div>
          </el-col>
        </el-row>
        <hr />
        <div>
          【题干】：
          <p style="color: blue" v-html="previewData.question"></p>
          <div v-if="previewData.questionType == 3">
            <span>单选题 选项：(一下选中的选项为正确答案)</span>
            <el-radio
              v-for="(item1,index) in previewData.options"
              :key="index"
              v-model="radio"
              :label="item1.isRight"
            >{{item1.title}}</el-radio>
          </div>
          <div v-if="previewData.questionType == 2">
            <div>多选题 选项：(一下选中的选项为确定答案)</div>
            <el-checkbox
              v-for="(item2,index) in previewData.options"
              :key="index"
              v-model="checkbox"
              :label="item2.isRight"
            >{{item2.title}}</el-checkbox>
          </div>
        </div>
        <hr />
        <div>
          【参考答案】：
          <el-button type="danger" size="mini" @click="initVideo">视频答案预览</el-button>
          <div class="video" v-show="flag">
            <video id="myVideo" class="video-js">
              <source :src="previewData.videoURL" />
            </video>
          </div>
        </div>
        <hr />
        <div>
          【答案解析】：
          <span v-html="previewData.answer"></span>
        </div>
        <hr />
        <div>
          【题目备注】：
          <span>{{previewData.remarks}}</span>
        </div>
        <hr />
      </div>
      <span slot="footer" class="dialog-footer">
        <el-button type="primary" @click="dialogVisibles">关闭</el-button>
      </span>
    </el-dialog>
  </div>
</template>

<script>
export default {
  props:['previewData','dialogVisible'],
  data(){
    return {
        flag: false, //视频显示区域
    }
  },
  methods:{
    // 关闭
    dialogVisibles(){
      this.$emit('dialogVisible',false)
    },
    // 视频播放
    initVideo() {
      this.flag = true;
      //初始化视频方法
      let myPlayer = this.$video(myVideo, {
      
        controls: true,
        //自动播放属性,muted:静音播放
        autoplay: "muted",
        //建议浏览器是否应在<video>加载元素后立即开始下载视频数据。
        preload: "auto",
        //设置视频播放器的显示宽度（以像素为单位）
        width: "400px",
        //设置视频播放器的显示高度（以像素为单位）
        height: "300px",
      });
    },
  }
};
</script>

<style scoped lang='less'></style>
