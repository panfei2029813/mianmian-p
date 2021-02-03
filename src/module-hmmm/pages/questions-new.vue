<template>
  <div class="questions-container">
    <div class="app-container">
      <el-card shadow="never">
        <!-- 标题 -->
        <div >试题录入</div>

        <!-- 分割线 -->
        <el-divider></el-divider>

        <!-- 表单内容 -->
          <el-form :model="qsNewForm" :rules="qsNewRules" ref="qsNewRef" label-width="100px">
            <!-- 学科 -->
            <el-form-item label="学科 :" prop="subjectID">
              <el-select v-model="qsNewForm.subjectID" @change="changeSubject" clearable placeholder="请选择">
                <el-option :label="item.label" :value="item.value" v-for="(item) in subjectsList" :key="item.value" ></el-option>
              </el-select>
            </el-form-item>

            <!-- 目录 -->
            <el-form-item label="目录 :" prop="catalogID">
              <el-select v-model="qsNewForm.catalogID" @focus="loadDirectorysList" no-data-text="无数据，请确认选择的学科" clearable placeholder="请选择">
                <el-option :label="item.directoryName" :value="item.id" v-for="(item) in directorysList" :key="item.id" ></el-option>
              </el-select>
            </el-form-item>

            <!-- 企业 -->
            <el-form-item label="企业 :" prop="enterpriseID">
              <el-select v-model="qsNewForm.enterpriseID"  @focus="loadCompanysList" clearable placeholder="请选择">
                <el-option :label="item.company" :value="item.id" v-for="(item,index) in companysList" :key="index"></el-option>
              </el-select>
            </el-form-item>

            <el-row >
              <!-- 城市 -->
                <el-form-item label="城市 :" prop="province" class="el-form-item-province">
                  <el-select v-model="qsNewForm.province" @change="changeProvince" filterable clearable class="city-select" placeholder="请选择">
                    <el-option :label="item" :value="item" v-for="(item,index) in cityList" :key="index"></el-option>
                  </el-select>
                </el-form-item>
              <!-- 地区 -->
                <el-form-item prop="city" class="el-form-item-province el-form-item-city">
                  <el-select v-model="qsNewForm.city" @focus="loadAreaList" no-data-text="无数据，请确认选择的城市" clearable class="city-select" placeholder="请选择" >
                  <el-option :label="item" :value="item" v-for="(item,index) in areaList" :key="index"></el-option>
                  </el-select>
                </el-form-item>
            </el-row>

            <!-- 方向 -->
            <el-form-item label="方向 :" prop="direction">
              <el-select v-model="qsNewForm.direction" clearable placeholder="请选择">
                <el-option  :label="item" :value="item" v-for="(item,index) in directionList" :key="index"></el-option>
              </el-select>
            </el-form-item>

            <!-- 题型 -->
            <el-form-item label="题型 :" prop="questionType">
              <el-radio-group v-model="qsNewForm.questionType" @change="changeQuestionType">
                <el-radio  :label="item.value" v-for="(item, index) in questionType" :key="index">{{item.label}}</el-radio>
              </el-radio-group>
            </el-form-item>

            <!-- 难度 -->
            <el-form-item label="难度 :" prop="difficulty">
              <el-radio-group v-model="qsNewForm.difficulty">
                <el-radio :label="item.value" v-for="(item, index) in difficulty" :key="index">{{item.label}}</el-radio>
              </el-radio-group>
            </el-form-item>

            <!-- 题干 -->
            <el-form-item label="题干 :" prop="question">
            <quill-editor v-model="qsNewForm.question"></quill-editor>
            </el-form-item>

            <!-- 选项 -->
            <el-form-item v-if="isShowQuestionType" label="选项 :" >
              <!-- 单选 -->
              <div v-if="isShowChecks===1">
                <el-row  class="question-type-row" v-for="(item,index) in qsNewForm.options" :key="index">
                  <el-col class="question-type-col" :span=".5" >
                    <el-radio v-model="radio" :label="item.code" @change="changeRadio">{{item.code}}:</el-radio>
                  </el-col>
                  <!-- 输入框 -->
                  <el-col class="question-type-col" :span="5">
                    <el-input v-model="item.title" placeholder="请输入内容"></el-input>
                  </el-col>
                  <!-- 图片上传 -->
                  <el-col :span="2">
                    <el-upload :slot-scope="item"
                      action="#"
                      list-type="picture-card"
                      :on-change="res=>{savePicture(res,item)}"
                      :auto-upload='false'
                      :limit="1"
                      ref="uploadImg"
                      class="avatar-uploader"
                    >
                      <div slot="file" slot-scope="{file}" >
                        <img
                          class="el-upload-list__item-thumbnail"
                          :src="file.url" alt=""
                        >
                        <span class="el-upload-list__item-actions">
                          <span
                            class="el-upload-list__item-preview"
                            @click="showPictureCardPreview(file)"
                          >
                            <i class="el-icon-zoom-in">预览</i>
                          </span>
                        </span>
                      </div>
                      <div slot="trigger" class="upImage">上传图片</div>
                      <i @click="removeImgURL(item)" class="el-icon-circle-close"></i>
                    </el-upload>
                    <el-dialog :visible.sync="isShowPictureCard">
                      <img width="100%" :src="dialogImageUrl" >
                    </el-dialog>
                  </el-col>
                </el-row>
              </div>
              <!-- 多选 -->
              <div v-else-if="isShowChecks===2">
                <el-row  class="question-type-row" v-for="(item,index) in qsNewForm.options" :key="index">
                  <el-col class="question-type-col" :span=".5" >
                    <el-checkbox v-model="item.isRight" >{{item.code}}:</el-checkbox>
                  </el-col>
                  <!-- 输入框 -->
                  <el-col class="question-type-col" :span="5">
                    <el-input v-model="item.title" placeholder="请输入内容"></el-input>
                  </el-col>
                  <!-- 图片上传 -->
                  <el-col :span="2">
                    <el-upload :slot-scope="item"
                      action="#"
                      list-type="picture-card"
                      :on-change="res=>{savePicture(res,item)}"
                      :auto-upload='false'
                      :limit="1"
                      ref="uploadImg"
                      class="avatar-uploader"
                    >
                      <div slot="file" slot-scope="{file}" >
                        <img
                          class="el-upload-list__item-thumbnail"
                          :src="file.url" alt=""
                        >
                        <span class="el-upload-list__item-actions">
                          <span
                            class="el-upload-list__item-preview"
                            @click="showPictureCardPreview(file)"
                          >
                            <i class="el-icon-zoom-in">预览</i>
                          </span>
                          <span
                            class="el-upload-list__item-delete"
                            @click="removeImgURL(item)"
                          >
                            <i class="el-icon-delete"></i>
                          </span>
                        </span>
                      </div>
                      <div slot="trigger" class="upImage">上传图片</div>
                    </el-upload>
                    <el-dialog :visible.sync="isShowPictureCard">
                      <img width="100%" :src="dialogImageUrl" >
                    </el-dialog>
                  </el-col>
                </el-row>
              </div>
              <el-button :disabled="isShowChecks===1" @click="addSeleAndQuest" type="danger" size="mini">+增加选项与答案</el-button>
            </el-form-item>

            <!-- 解析视频 -->
            <el-form-item label="解析视频 :" prop="videoURL">
              <el-input v-model="qsNewForm.videoURL" class="w510" placeholder="请输入内容"></el-input>
            </el-form-item>

             <!-- 答案解析 -->
            <el-form-item label="答案解析 :" prop="answer">
            <quill-editor v-model="qsNewForm.answer"></quill-editor>
            </el-form-item>

            <!-- 题目备注 -->
            <el-form-item label="题目备注 :" >
              <el-input type="textarea"  v-model="qsNewForm.remarks" :autosize="{ minRows: 2, maxRows: 4}" class="w510" placeholder="请输入内容" ></el-input>
            </el-form-item>

            <!-- 试题标签 -->
            <el-form-item label="试题标签 :"  >
              <el-select v-model="qsNewForm.tags" @focus="loadTagsList" multiple clearable no-data-text="无数据，请确认选择的学科" placeholder="请选择">
                  <el-option
                    v-for="(item,index) in tagsList"
                    :key="index"
                    :label="item.tagName"
                    :value="item.tagName">
                  </el-option>
              </el-select>
            </el-form-item>

            <!-- 提交表单 -->
            <el-form-item>
              <el-button type="primary" @click="submitForm('qsNewRef')">确认提交</el-button>
              <el-button @click="resetForm">重置</el-button>
            </el-form-item>
          </el-form>
      </el-card>
    </div>
  </div>
</template>

<script>
// 引入接口方法
import { simple as getSubjectsList } from '@/api/hmmm/subjects'
import { list as getDirectorysList } from '@/api/hmmm/directorys'
import { list as getCompanysList } from '@/api/hmmm/companys'
import { list as getTagsList } from '@/api/hmmm/tags'
import { add as addformQsNewData } from '@/api/hmmm/questions'
// 引入富文本相关
import 'quill/dist/quill.core.css'
import 'quill/dist/quill.snow.css'
import 'quill/dist/quill.bubble.css'
import { quillEditor } from 'vue-quill-editor'
// 引入 获取城市 地区的方法
import { provinces, citys } from '@/api/hmmm/citys'
// 引入方向、题型、难度 数据
import { direction, questionType, difficulty } from '@/api/hmmm/constants'

export default {
  name: 'questionsNew',
  components: {
    quillEditor
  },
  props: {

  },
  data () {
    const videoURLPass = (rule, value, callback) => { // 试试验证URL格式自定义校验
      const regURL = /[a-zA-z]+:\/\/[^\s]*/
      if (regURL.test(value) || value === '') {
        callback()
      } else {
        callback(new Error('URL地址格式不正确'))
      }
    }
    return {
      subjectsList: [], // 学科列表
      directorysList: [], // 目录列表
      companysList: [], // 企业列表
      cityList: [], // 城市列表
      areaList: [], // 地区列表
      directionList: [], // 方向列表
      questionType: [], // 题型列表
      difficulty: [], // 难度列表
      tagsList: [], // 标签列表

      isShowChecks: '', // 控制显示单选或多选
      isShowQuestionType: false, // 控制选项盒子的显示与隐藏
      isShowPictureCard: false, // 控制预览图片的显示与隐藏
      dialogImageUrl: '', // 预览图片的临时地址
      radio: 'A', // 双向绑定当前单选项，选择的值
      tagsFrom: '', // 临时存放标签

      qsNewForm: { // 表单上传的数据
        subjectID: '', // 学科
        catalogID: '', // 目录
        enterpriseID: '', // 企业
        province: '', // 城市
        city: '', // 地区
        direction: '', // 方向
        questionType: '', // 题型
        difficulty: '', // 难度
        question: '', // 题干
        options: [ // 选项
          {
            code: 'A', // 代码 A
            title: '', // 标题
            img: '', // 图片URL
            isRight: true // 是否正确答案
          },
          {
            code: 'B', // 代码 B
            title: '', // 标题
            img: '', // 图片URL
            isRight: false // 是否正确答案
          },
          {
            code: 'C', // 代码 C
            title: '', // 标题
            img: '', // 图片URL
            isRight: false // 是否正确答案
          },
          {
            code: 'D', // 代码 D
            title: '', // 标题
            img: '', // 图片URL
            isRight: false // 是否正确答案
          }
        ],
        videoURL: '', // 解析视频
        answer: '', // 答案解析
        remarks: '', // 题目备注
        tags: '' // 标签
      },

      qsNewRules: { // 表单验证规则
        subjectID: [
          { required: true, message: '请选择学科', trigger: 'change' }
        ],
        catalogID: [
          { required: true, message: '请选择目录', trigger: 'change' }
        ],
        enterpriseID: [
          { required: true, message: '请选择企业', trigger: 'change' }
        ],
        province: [
          { required: true, message: '请选择城市', trigger: 'change' }
        ],
        city: [
          { required: true, message: '请选择地区', trigger: 'change' }
        ],
        direction: [
          { required: true, message: '请选择方向', trigger: 'change' }
        ],
        questionType: [
          { required: true, message: '请选择题型', trigger: 'change' }
        ],
        difficulty: [
          { required: true, message: '请选择难度', trigger: 'change' }
        ],
        question: [
          { required: true, message: '请填写题干', trigger: 'blur' }
        ],
        videoURL: [
          { validator: videoURLPass, trigger: 'blur' }
        ],
        answer: [
          { required: true, message: '请填写答案解析', trigger: 'blur' }

        ]
      }
    }
  },
  created () {
    this.loadSubjectsList() // 加载 学科列表

    this.cityList = provinces() // 获取 城市列表
    this.directionList = direction // 获取 方向列表
    this.questionType = questionType // 获取 题型列表
    this.difficulty = difficulty // 获取 难度列表
  },
  watch: {
    'qsNewForm.question': function () {
      this.$refs.qsNewRef.validateField('question')
    },
    'qsNewForm.answer': function () {
      this.$refs.qsNewRef.validateField('answer')
    }
  },
  methods: {
    async loadSubjectsList () { // 获取学科 列表
      const { data } = await getSubjectsList()
      this.subjectsList = data.reverse()
    },

    changeSubject () { 
      this.qsNewForm.catalogID = ''
      this.qsNewForm.tags = ''
    },

    async loadDirectorysList () { // 获取目录列表
      const query = { subjectID: this.qsNewForm.subjectID, pagesize: 20 }
      const { data } = await getDirectorysList(query)
      this.directorysList = data.items.reverse()
    },

    async loadCompanysList () { // 获取企业列表
      // 1. 请求获取数据
      const { data } = await getCompanysList({
        // city: this.qsNewForm.city,
        pagesize: 50
      })
      // 2. 将数据添加到列表中
      this.companysList = data.items.reverse()
      // 3. 判断是否还有数据
      if (data.items.length !== 0) {
        return false
      } else {
        // 有就更新获取下一页的数据页码
      }
    },

    changeProvince () { 
      this.qsNewForm.city = ''
    },

    loadAreaList () { // 根据选择的城市 获取对应地区列表
      this.areaList = citys(this.qsNewForm.province)
    },

    changeQuestionType (cback) { // 题型值改变时触发，控制选项的变化 cb回调选择的值
      this.isShowChecks = cback // 控制单多选切换
      // 切换后初始化选项
      this.changeRadio('A')
      this.qsNewForm.options.length = 4
      if (cback === 3) { // 控制选项是否显示
        this.isShowQuestionType = false
      } else {
        this.isShowQuestionType = true
      }
    },

    addSeleAndQuest () {
      const codeStr = this.qsNewForm.options[this.qsNewForm.options.length - 1].code
      let num = codeStr.charCodeAt() // 将ASC码 转换 为数字
      num++
      if (num < 72) {
        this.qsNewForm.options.push({
          code: String.fromCharCode(num),
          title: '',
          img: '',
          isRight: false
        })
      } else {
        this.$message({
          message: '已达上限',
          type: 'warning'
        })
      }
    },

    changeRadio (cback) { // 单选项改变时，修改正确答案的值
      try {
        this.qsNewForm.options.forEach(t => {
          if (t.code === cback) {
            t.isRight = true
          } else {
            t.isRight = false
          }
        })
      } catch (err) {
        console.error(err)
      }
    },

    async loadTagsList () { // 获取对应学科标签
      try {
        const { data } = await getTagsList({ subjectID: this.qsNewForm.subjectID })
        this.tagsList = data.items
      } catch (err) {
        console.error(err)
      }
    },

    async submitForm (formName) { // 提交表单
      this.$refs[formName].validate(async (valid) => { 
        if (valid) { // 执行提交
          try {
            const formData = this.qsNewForm
            formData.tags = formData.tags.join(',') // 将标签的数组转为字符串,并赋值给from数据
            formData.questionType = formData.questionType.toString()
            formData.difficulty = formData.difficulty.toString()
            await addformQsNewData(formData)
            // 初始化
            this.resetForm()
            this.$message({
              message: '提交成功',
              type: 'success'
            })
          } catch (err) {
            console.log(err)
            this.$message({
              message: '提交失败',
              type: 'error'
            })
          }
        } else { // 表单验证未通过
          this.$message({
            message: '未填写必填项',
            type: 'warning'
          })
        }
      })
    },

    resetForm () { // 重置表单
      try {
        this.$refs.qsNewRef.resetFields()
        this.qsNewForm.remarks = ''
        this.qsNewForm.tags = ''
        this.isShowChecks = ''
        this.qsNewForm.options = [{ code: 'A', title: '', img: '', isRight: true }, { code: 'B', title: '', img: '', isRight: false }, { code: 'C', title: '', img: '', isRight: false }, { code: 'D', title: '', img: '', isRight: false }]
      } catch (err) {
        console.error(err)
      }
    },

    savePicture (file, item) { // 上传图片时，保存图片地址
      try {
        item.img = file.url
        this.$message({
          message: '图片上传成功',
          type: 'success'
        })
      } catch (err) {
        this.$message({
          message: '图片上传失败',
          type: 'warning'
        })
      }
    },

    showPictureCardPreview (file) { // 预览图片
      try {
        this.dialogImageUrl = file.url
        this.isShowPictureCard = true
      } catch (err) {
        console.error(err)
      }
    },

    removeImgURL (item) { // 清除对应index的图片存放地址
      try {
        const index = item.code.charCodeAt() - 65 // 获取到当前上传图片选项的index
        if (this.$refs.uploadImg[index].uploadFiles.length !== 0) {
          item.img = ''
          this.$refs.uploadImg[index].clearFiles() // 清除对应index的图片预览
          this.$message({
            message: '图片移除成功',
            type: 'success'
          })
        }
      } catch (err) {
        console.log(err)
        this.$message({
          message: '图片移除失败',
          type: 'warning'
        })
      }
    }

  }
}
</script>

<style lang="less" scoped>

.el-divider { // 标题 下方分割线的样式
  margin-top: 12px;
  margin-bottom: 20px;
}

.el-select{ // 所有选择框的宽度
  width: 510px;
}

// 城市地区选择框 相关样式
.city-select {
  width: 249px;
}
.el-form-item-province {
  display: inline-block;
}
/deep/.el-form-item-city {
  .el-form-item__content {
    margin-left: 10px!important;
  }
}

// 选项内 各行间距 的相关样式
.question-type-row .el-col {
  padding-left: 15px;
  padding-bottom: 15px;
}
.question-type-col {
  padding-top: 10px;
}

// 上传图片相关样式
.avatar-uploader {
  position: relative;
  /deep/.el-upload{ // 上传图片父盒子
    width: 120px;
    height: 54px;
    border: 1px dashed #d9d9d9;
    border-radius: 5px;
    cursor: pointer;
    overflow: hidden;
  }

  .upImage { // 上传图片按钮
    width: 120px;
    height: 54px;
    line-height: 54px;
  }
  /deep/.el-upload-list--picture-card { // 上传后图片显示的位置
    position: absolute;
    top: 0;
    left: 0;
    .el-upload-list__item { // 上传后图片的大小
      width: 120px;
      height: 54px;
    }
  }
  .el-icon-circle-close { // 删除图片按钮
    position: absolute;
    top: -5px;
    right: -15px;
    font-size: 16px;
    color: #999;
  }
}

/deep/.ql-editor { // 题目备注的最小高度
  min-height: 180px;
}

.w510 {
  width: 510px;
}

</style>