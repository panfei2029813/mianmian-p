<template>
<<<<<<< HEAD
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
=======
  <div>
    <el-card class="box-card">
      <!-- 头部 -->
      <!-- 新增试题 -->
      <div class="header">
        <!-- 左边文字 -->
        <span>
          说明：目前支持学科和关键字条件筛选
        </span>
        <!-- 左边文字 -->
        <!-- 右边按钮 -->
        <el-button type="success" icon="el-icon-edit" @click="addQuestions">
          新增试题
        </el-button>
        <!-- 右边按钮 -->
      </div>
      <!-- 新增试题 -->

      <!-- 表单部分 -->

      <el-form
        label-position="right"
        :model="formInline"
        class="demo-form-inline"
      >
        <!-- 第一行 -->
        <el-row>
          <!-- 学科 -->
          <el-col :span="6">
            <el-form-item label="学科" label-width="80px">
              <el-select
                v-model="formInline.subjectsId"
                placeholder="请选择活动区域"
                @change="onSubjectsChange(formInline.subjectsId)"
                style="width: 100%;"
              >
                <el-option
                  v-for="(item, index) in subjectsOptions"
                  :key="index"
                  :label="item.label"
                  :value="item.value"
                ></el-option>
              </el-select>
            </el-form-item>
          </el-col>
          <!-- 学科 -->

          <!-- 二级目录 -->
          <el-col :span="6">
            <el-form-item label="二级目录" label-width="80px">
              <el-select
                v-model="formInline.second"
                placeholder="请选择活动区域"
                style="width: 100%;"
              >
                <el-option
                  v-for="(item, index) in seconItems"
                  :key="index"
                  :label="item.label"
                  :value="item.value"
                ></el-option>
              </el-select>
            </el-form-item>
          </el-col>
          <!-- 二级目录 -->

          <!-- 标签 -->
          <el-col :span="6"
            ><el-form-item label="标签" label-width="80px">
              <el-select
                v-model="formInline.tag"
                placeholder="请选择活动区域"
                style="width: 100%;"
              >
                <el-option
                  v-for="(item, index) in tags"
                  :key="index"
                  :label="item.label"
                  :value="item.value"
                ></el-option>
              </el-select> </el-form-item
          ></el-col>
          <!-- 标签 -->

          <!-- 关键字 -->
          <el-col :span="6"
            ><el-form-item label="关键字" label-width="80px">
              <el-input
                style="width: 100%;"
                v-model="formInline.keyword"
                placeholder="根据题干搜索"
              ></el-input> </el-form-item
          ></el-col>
          <!-- 关键字 -->
        </el-row>
        <!-- 第一行 -->

        <!-- 第二行 -->
        <el-row>
          <!-- 试题类型 -->
          <el-col :span="6">
            <el-form-item label="试题类型" label-width="80px">
              <el-select
                style="width: 100%;"
                v-model="formInline.questionTypes"
                placeholder="请选择活动区域"
                @change="onSubjectsChange(formInline.subjectsId)"
              >
                <el-option
                  v-for="(item, index) in questionType"
                  :key="index"
                  :label="item.label"
                  :value="item.value"
                ></el-option>
              </el-select>
            </el-form-item>
          </el-col>
          <!-- 试题类型 -->

          <!-- 难度 -->
          <el-col :span="6">
            <el-form-item label="难度" label-width="80px">
              <el-select
                style="width: 100%;"
                v-model="formInline.difficult"
                placeholder="请选择活动区域"
              >
                <el-option
                  v-for="(item, index) in difficulty"
                  :key="index"
                  :label="item.label"
                  :value="item.value"
                ></el-option>
              </el-select>
            </el-form-item>
          </el-col>
          <!-- 难度 -->

          <!-- 方向 -->
          <el-col :span="6"
            ><el-form-item label="方向" label-width="80px">
              <el-select
                v-model="formInline.direct"
                placeholder="请选择活动区域"
                style="width: 100%;"
              >
                <el-option
                  v-for="(item, index) in direction"
                  :key="index"
                  :label="item"
                  :value="item"
                ></el-option>
              </el-select> </el-form-item
          ></el-col>
          <!-- 方向 -->

          <!-- 录入人 -->
          <el-col :span="6">
            <el-form-item label="录入人" label-width="80px">
              <el-select
                v-model="formInline.edit"
                placeholder="请选择活动区域"
                style="width: 100%;"
              >
                <el-option
                  v-for="(item, index) in editor"
                  :key="index"
                  :label="item.label"
                  :value="item.value"
                ></el-option>
              </el-select>
            </el-form-item>
          </el-col>
          <!-- 录入人 -->
        </el-row>
        <!-- 第二行 -->

        <!-- 第三行 -->
        <el-row>
          <!-- 题目备注 -->
          <el-col :span="6"
            ><el-form-item label="题目备注 " label-width="80px">
              <el-input
                style="width: 100%;"
                v-model="formInline.questionNode"
                placeholder="根据题干搜索"
              ></el-input> </el-form-item
          ></el-col>
          <!-- 题目备注 -->

          <!-- 企业简称 -->
          <el-col :span="6"
            ><el-form-item label="企业简称 " label-width="80px">
              <el-input
                style="width: 100%;"
                v-model="formInline.shortName"
                placeholder="根据题干搜索"
              ></el-input> </el-form-item
          ></el-col>
          <!-- 企业简称 -->

          <!-- 城市 -->
          <el-col :span="6">
            <el-form-item label="城市" label-width="80px">
              <el-select
                v-model="formInline.citys"
                placeholder="请选择活动区域"
                style="width:48%; margin-right:2%;"
                @change="hasCity"
              >
                <el-option
                  v-for="(item, index) in citys"
                  :key="index"
                  :label="item"
                  :value="item"
                ></el-option>
              </el-select>

              <el-select
                v-model="formInline.cities"
                placeholder="请选择活动区域"
                style="width:50%; "
              >
                <el-option
                  v-for="(item, index) in cities"
                  :key="index"
                  :label="item"
                  :value="item"
                ></el-option>
              </el-select>
            </el-form-item>
          </el-col>
          <!-- 城市 -->

          <!-- 录入人 -->
          <el-col :span="6">
            <el-form-item label-width="80px" class="search">
              <el-button type="primary" class="buttons" @click="onSearch"
                >搜索</el-button
              >
              <el-button class="buttons" @click="resetForm">清除</el-button>
            </el-form-item>
          </el-col>
          <!-- 录入人 -->
        </el-row>
        <!-- 第三行 -->
      </el-form>

      <!-- 表单部分 -->
      <!-- 总数 -->
      <el-row>
        <el-col :span="24"
          ><div class="grid-content-total bg-purple-dark">
            <i class="el-icon-info"></i>
            数据一共 {{ total }} 条
          </div></el-col
        >
      </el-row>
      <!-- 总数 -->

      <!-- 表格 -->
      <!-- 表格 -->
      <el-table :data="tableData" style="width: 100%" ref="removeId">
        <el-table-column
          prop="number"
          label="试题编号"
          width="120px
        "
        >
        </el-table-column>
        <el-table-column prop="subject" label="学科" width="80">
        </el-table-column>
        <el-table-column prop="catalog" label="目录" width="120">
        </el-table-column>
        <el-table-column prop="question" label="题干">
          <template slot-scope="scope">
            {{ scope.row.question | html2Text }}
          </template>
        </el-table-column>
        <el-table-column prop="addDate" label="录入时间">
          <template slot-scope="scope">
            {{ scope.row.addDate | parseTime }}
          </template>
        </el-table-column>
        <el-table-column prop="difficulty" label="难度">
          <template slot-scope="scope">
            <span v-if="scope.row.difficulty === 1">真他妈简单</span>
            <span v-else-if="scope.row.difficulty === 2">一般般拉</span>
            <span v-else>卧槽好难</span>
          </template>
        </el-table-column>
        <el-table-column prop="creator" label="录入人"> </el-table-column>
        <el-table-column prop="address" label="操作" width="250">
          <template slot-scope="scope">
            <el-tooltip
              class="item"
              effect="light"
              content="预览"
              placement="bottom-end"
              visible-arrow="true"
            >
              <el-button
                type="primary"
                plain
                icon="el-icon-view"
                circle
                @click="onPre(scope.row)"
              ></el-button>
            </el-tooltip>

            <el-tooltip
              class="item"
              effect="light"
              content="修改"
              placement="bottom-end"
              visible-arrow="false"
            >
              <el-button
                type="success"
                plain
                icon="el-icon-edit"
                circle
                @click="onModification"
              ></el-button>
            </el-tooltip>

            <el-tooltip
              class="item"
              effect="light"
              content="删除"
              placement="bottom-end"
              visible-arrow="false"
            >
              <el-button
                type="danger"
                plain
                icon="el-icon-delete"
                circle
                @click="onRemove(scope.row)"
              ></el-button>
            </el-tooltip>

            <el-tooltip
              class="item"
              effect="light"
              content="优选"
              placement="bottom-end"
              visible-arrow="false"
            >
              <el-button
                type="warning"
                plain
                icon="el-icon-check"
                circle
                @click="choose(scope.row)"
              ></el-button>
            </el-tooltip>
          </template>
        </el-table-column>
      </el-table>
      <!-- 表格 -->
      <!-- 表格 -->

      <!-- 头部 -->

      <div class="block">
        <el-pagination
          @size-change="handleSizeChange"
          @current-change="handleCurrentChange"
          :current-page="query.page"
          :page-sizes="[5, 20, 50, 100]"
          :page-size="5"
          layout="  prev, pager, next, sizes,jumper"
          :total="total"
        >
        </el-pagination>
      </div>
    </el-card>
>>>>>>> 06743ea1b99bcb79601238e6fdf288ab44946325
  </div>
</template>

<script>
<<<<<<< HEAD
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
=======
import { simple as dirSimple } from '@/api/hmmm/directorys.js'
import { simple as subSimple } from '@/api/hmmm/subjects.js'
import { list, remove, choiceAdd } from '@/api/hmmm/questions.js'
import { simple as tagsSimple } from '@/api/hmmm/tags.js'
import { provinces, citys } from '@/api/hmmm/citys.js'
import { parseTime, html2Text } from '@/utils/index.js'
import {
  difficulty,
  questionType,
  direction,
  editor
} from '@/api/hmmm/constants.js'
export default {
  data() {
    return {
      query: {
        page: 0,
        pagesize: 10,
        subjectID: 0,
        directory: '',
        state: '',
        direct: '',
        keyword: ''
      },
      tableQuery: {
        page: 0,
        pagesize: 5,
        subjectID: null,
        keyword: ''
      },
      // 表单数据
      formInline: {
        subjectsId: '',
        second: '',
        tag: '',
        keyword: '',
        difficult: '',
        edit: '',
        questionTypes: '',
        citys: '',
        cities: '',
        shortName: '',
        questionNode: ''
      },
      objectId: 0,

      // 学科列表数据
      subjectsOptions: [],

      // 二级目录
      seconItems: [],
      // 标签
      tags: [],
      // 试题类型
      questionType: questionType,
      // 难易程度
      difficulty: difficulty,
      // 方向
      direction: direction,
      // 录入者
      editor: editor,
      citys: [],
      cities: [],
      tableData: [],
      chooseQuery: {
        choiceState: 0,
        id: 0
      },
      total: 0
    }
  },
  created() {
    this.lodeQuestion()
  },
  mounted() {},
  methods: {
    async lodeQuestion() {
      this.hasTableDdata()
      this.citys = provinces()
      this.cities = provinces()
      const { data } = await subSimple()
      this.subjectsOptions = data
    },
    async onSubjectsChange(el) {
      this.formInline.second = ''
      this.query.subjectID = el
      const { data } = await dirSimple(this.query)
      const tagData = await tagsSimple(this.query)
      this.tags = tagData.data
      this.seconItems = data
    },
    hasCity(el) {
      this.cities = citys(el)
    },
    async hasTableDdata() {
      const data = await list(this.tableQuery)
      console.log(data.data)
      this.tableData = data.data.items
      this.total = data.data.counts
    },
    // 预览
    onPre(el) {
      console.log(el.choiceState)
    },
    // 修改按钮
    onModification() {
      this.$router.push('/questions/choice')
    },
    // 删除
    async onRemove(row) {
      await remove(row)
      this.lodeQuestion()
    },
    // 优选
    async choose(el) {
      this.chooseQuery.id = el.id
      el.choiceState = 1
      console.log(el)
      await choiceAdd(this.chooseQuery)
    },
    onSearch() {
      this.tableQuery.keyword = this.formInline.keyword
      this.tableQuery.subjectID = this.formInline.subjectID
      console.log(this.tableQuery, 321)
      this.hasTableDdata()
    },
    handleCurrentChange(el) {
      this.tableQuery.page = el
      this.hasTableDdata()
    },
    handleSizeChange(el) {
      this.tableQuery.pagesize = el
      this.hasTableDdata()
    },
    addQuestions() {
      this.$router.push('/questions/new')
    },
    resetForm() {
      this.formInline = {}
    }
  },
  filters: {
    html2Text,
    parseTime
  }
}
>>>>>>> 06743ea1b99bcb79601238e6fdf288ab44946325
</script>

<style scoped lang="less">
.box-card {
  margin-bottom: 20px;
}
.header {
  display: flex;
  justify-content: space-between;
  margin-bottom: 10px;
  span {
    color: rgb(248, 169, 169);
    font-size: 16px;
  }
}
.search {
  /deep/ .el-form-item__content {
    display: flex;
    justify-content: flex-end;
  }
}
.grid-content-total {
  color: #666;
  background-color: #f4f4f5;
  padding: 8px 16px;
  border-radius: 2px;
  margin-bottom: 20px;
}
.block {
  float: right;
}
/deep/.item {
  border: none;
}
</style>
