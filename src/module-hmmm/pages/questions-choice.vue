<template>
  <div class="questionsContainer">
    <el-card class="box-card">
      <!-- 头部 -->
      <div class="top">
        <span>说明：目前支持学科和关键字条件筛选</span>
        <el-button type="success" icon="el-icon-edit" size="small" @click="$router.push(`/questions/new`)"
          >新增试题</el-button
        >
      </div>
      <!-- /头部 -->
      <!-- 条件筛选区域 -->
      <div class="select">
        <el-form :model="form" label-width="80px" ref="form">
          <el-row>
            <el-col :span="6"
              ><el-form-item label="学科" prop="subject">
                <el-select
                  v-model="form.subject"
                  placeholder="请选择"
                  size="small"
                  @change="choseSubject(form.subject)"
                >
                  <el-option
                    v-for="(item, index) in subjectSimple"
                    :key="index"
                    :label="item.label"
                    :value="item.value"
                  ></el-option>
                </el-select> </el-form-item
            ></el-col>
            <el-col :span="6"
              ><el-form-item label="二级目录" prop="directory">
                <el-select
                  v-model="form.directory"
                  placeholder="请选择"
                  size="small"
                >
                  <el-option
                    :key="item.id"
                    v-for="item in directorys"
                    :label="item.directoryName"
                    :value="item.id"
                  ></el-option>
                </el-select> </el-form-item
            ></el-col>
            <el-col :span="6"
              ><el-form-item label="标签" prop="tags">
                <el-select
                  v-model="form.tags"
                  placeholder="请选择"
                  size="small"
                >
                  <el-option
                    :key="item.id"
                    v-for="item in tags"
                    :label="item.tagName"
                    :value="item.id"
                  ></el-option>
                </el-select> </el-form-item
            ></el-col>
            <el-col :span="6"
              ><el-form-item label="关键字" prop="question">
                <el-input
                  v-model="form.question"
                  placeholder="根据题干搜索"
                  size="small"
                ></el-input> </el-form-item
            ></el-col>
            <el-col :span="6"
              ><el-form-item label="试题类型" prop="questionType">
                <el-select
                  v-model="form.questionType"
                  placeholder="请选择"
                  size="small"
                >
                  <el-option label="单选" value="1"></el-option>
                  <el-option label="多选" value="2"></el-option>
                  <el-option label="简答" value="3"></el-option>
                </el-select> </el-form-item
            ></el-col>
            <el-col :span="6"
              ><el-form-item label="难度" prop="difficulty">
                <el-select
                  v-model="form.difficulty"
                  placeholder="请选择"
                  size="small"
                >
                  <el-option label="简单" value="1"></el-option>
                  <el-option label="一般" value="2"></el-option>
                  <el-option label="困难" value="3"></el-option>
                </el-select> </el-form-item
            ></el-col>
            <el-col :span="6"
              ><el-form-item label="方向" prop="direction">
                <el-select
                  v-model="form.direction"
                  placeholder="请选择"
                  size="small"
                >
                  <el-option label="o2o" value="0"></el-option>
                  <el-option label="外包服务" value="1"></el-option>
                  <el-option label="企业服务" value="2"></el-option>
                  <el-option label="互联网金融" value="3"></el-option>
                  <el-option label="企业咨询" value="4"></el-option>
                  <el-option label="互联网" value="5"></el-option>
                  <el-option label="电子商务" value="6"></el-option>
                  <el-option label="其他" value="7"></el-option>
                </el-select> </el-form-item
            ></el-col>
            <el-col :span="6"
              ><el-form-item label="录入人" prop="creator">
                <el-select
                  v-model="form.creator"
                  placeholder="请选择"
                  size="small"
                >
                  <el-option
                    :key="item.id"
                    v-for="item in usersList"
                    :label="item.username"
                    :value="item.id"
                  ></el-option>
                </el-select> </el-form-item
            ></el-col>
            <el-col :span="6"
              ><el-form-item label="题目备注" prop="name">
                <el-input
                  v-model="form.name"
                  size="small"
                ></el-input> </el-form-item
            ></el-col>
            <el-col :span="6"
              ><el-form-item label="企业简称" prop="name">
                <el-input
                  v-model="form.name"
                  size="small"
                ></el-input> </el-form-item
            ></el-col>
            <el-col :span="6"
              ><el-form-item label="城市" class="city" prop="city">
                <el-select
                  v-model="form.city"
                  placeholder="请选择"
                  size="small"
                  class="one"
                >
                  <el-option
                    :key="index"
                    v-for="(item, index) in cityList"
                    :label="item"
                    :value="index"
                    ><span
                      @click="changeCity(item)"
                      style="width: 100%;display: inline-block"
                    >
                      {{ item }}</span
                    ></el-option
                  >
                </el-select>
                <el-select
                  v-model="form.area"
                  placeholder="请选择"
                  size="small"
                  class="two"
                >
                  <el-option
                    :key="index"
                    v-for="(item, index) in areaList"
                    :label="item"
                    :value="index"
                  ></el-option>
                  <el-option label="区域二" value="beijing"></el-option>
                </el-select> </el-form-item
            ></el-col>
            <el-col :span="6"
              ><el-form-item style="float:right">
                <el-button size="small" @click="reset">清除</el-button>
                <el-button
                  type="primary"
                  @click="onSubmit(form.subject, form.question)"
                  size="small"
                  >搜索</el-button
                >
              </el-form-item></el-col
            ></el-row
          >
        </el-form>
      </div>
      <!-- /条件筛选区域 -->
      <!--数据展示区域 -->
      <div class="container">
        <template>
          <el-tabs v-model="activeName" type="card" @tab-click="handleClick">
            <el-tab-pane label="全部" name="first">
              <el-alert
                type="info"
                class="total"
                :closable="false"
                show-icon
                :title="alertext"
              >
              </el-alert>
              <template>
                <el-table :data="tableData" style="width: 100%">
                  <el-table-column prop="number" label="试题编号" width="120">
                  </el-table-column>
                  <el-table-column prop="subject" label="学科" width="120">
                  </el-table-column>
                  <el-table-column prop="catalog" label="目录" width="120">
                  </el-table-column>
                  <el-table-column prop="questionType" label="题型" width="120">
                    <template slot-scope="scope">
                      <span v-if="scope.row.questionType === '1'">单选</span>
                      <span v-else-if="scope.row.questionType === '2'"
                        >多选</span
                      >
                      <span v-else>简答</span>
                    </template>
                  </el-table-column>
                  <el-table-column prop="question" label="题干" width="280">
                    <template slot-scope="scope">
                      <div>
                        {{ scope.row.question | textFormat }}
                      </div>
                    </template>
                  </el-table-column>
                  <el-table-column prop="addDate" label="录入时间" width="180">
                    <template slot-scope="scope">
                      <div>
                        {{ scope.row.addDate | dateFormat }}
                      </div>
                    </template>
                  </el-table-column>
                  <el-table-column prop="difficulty" label="难度" width="80">
                    <template slot-scope="scope">
                      <span v-if="scope.row.difficulty === '1'">简单</span>
                      <span v-else-if="scope.row.difficulty === '2'">一般</span>
                      <span v-else>困难</span>
                    </template>
                  </el-table-column>
                  <el-table-column prop="creator" label="录入人" width="120">
                  </el-table-column>
                  <el-table-column prop="chkState" label="审核状态" width="120">
                    <template slot-scope="scope">
                      <span v-if="scope.row.chkState === 0">待审核</span>
                      <span v-else-if="scope.row.chkState === 1">已审核</span>
                      <span v-else>已拒绝</span>
                    </template>
                  </el-table-column>
                  <el-table-column
                    prop="chkRemarks"
                    label="审核意见"
                    width="150"
                  >
                  </el-table-column>
                  <el-table-column prop="chkUser" label="审核人" width="120">
                  </el-table-column>
                  <el-table-column prop="chkState" label="发布状态" width="150">
                    <template slot-scope="scope">
                      <span v-if="scope.row.publishState === 1">待发布</span>
                      <span v-else-if="scope.row.publishState === 2">已发布</span>
                      <span v-else>已下架</span>
                    </template>
                  </el-table-column>
                  <el-table-column label="操作" fixed="right" width="200">
                    <template class="btnText" slot-scope="scope">
                      <el-button
                        type="text"
                        @click="questionsPreview(scope.row)"
                        >预览</el-button
                      >
                      <el-button
                        type="text"
                        @click="chkStateChange(scope.row.id)"
                        :disabled="
                          scope.row.chkState === 0 ? false : 'disabled'
                        "
                        >审核</el-button
                      >
                      <el-button
                        type="text"
                        @click="$router.push(`/questions/new?id=${scope.row.id}`)"
                        :disabled="
                          scope.row.publishState === 1 ? false : 'disabled'
                        "
                        >修改</el-button
                      >
                      <el-button
                        type="text"
                        @click="open(scope.row.id, scope.row.publishState)"
                        >{{
                          scope.row.publishState === 0 ? '上架' : '下架'
                        }}</el-button
                      >
                      <el-button
                        type="text"
                        :disabled="
                          scope.row.publishState === 1 ? false : 'disabled'
                        "
                        @click="deleteQuestion(scope.row)"
                        >删除</el-button
                      >
                    </template>
                  </el-table-column>
                </el-table>
              </template>
            </el-tab-pane>
            <el-tab-pane label="待审核" name="second">
              <template>
                <el-table :data="toBeReviewed" style="width: 100%">
                  <el-table-column prop="number" label="试题编号" width="120">
                  </el-table-column>
                  <el-table-column prop="subject" label="学科" width="120">
                  </el-table-column>
                  <el-table-column prop="catalog" label="目录" width="120">
                  </el-table-column>
                  <el-table-column prop="questionType" label="题型" width="120">
                    <template slot-scope="scope">
                      <span v-if="scope.row.questionType === '1'">单选</span>
                      <span v-else-if="scope.row.questionType === '2'"
                        >多选</span
                      >
                      <span v-else>简答</span>
                    </template>
                  </el-table-column>
                  <el-table-column prop="question" label="题干" width="280">
                    <template slot-scope="scope">
                      <div>
                        {{ scope.row.question | textFormat }}
                      </div>
                    </template>
                  </el-table-column>
                  <el-table-column prop="addDate" label="录入时间" width="180">
                    <template slot-scope="scope">
                      <div>
                        {{ scope.row.addDate | dateFormat }}
                      </div>
                    </template>
                  </el-table-column>
                  <el-table-column prop="difficulty" label="难度" width="80">
                    <template slot-scope="scope">
                      <span v-if="scope.row.difficulty === '1'">简单</span>
                      <span v-else-if="scope.row.difficulty === '2'">一般</span>
                      <span v-else>困难</span>
                    </template>
                  </el-table-column>
                  <el-table-column prop="creator" label="录入人" width="120">
                  </el-table-column>
                  <el-table-column prop="chkState" label="审核状态" width="120">
                    <template slot-scope="scope">
                      <span v-if="scope.row.chkState === 0">待审核</span>
                      <span v-else-if="scope.row.chkState === 1">已审核</span>
                      <span v-else>已拒绝</span>
                    </template>
                  </el-table-column>
                  <el-table-column
                    prop="chkRemarks"
                    label="审核意见"
                    width="150"
                  >
                  </el-table-column>
                  <el-table-column prop="chkUser" label="审核人" width="120">
                  </el-table-column>
                  <el-table-column prop="chkState" label="发布状态" width="150">
                    <template slot-scope="scope">
                      <span v-if="scope.row.chkState !== 1">待发布</span>
                      <span v-else-if="scope.row.chkState === 1">已发布</span>
                    </template>
                  </el-table-column>
                  <el-table-column label="操作" fixed="right" width="200">
                    <template class="btnText" slot-scope="scope">
                      <el-button
                        type="text"
                        @click="questionsPreview(scope.row)"
                        >预览</el-button
                      >
                      <el-button
                        type="text"
                        :disabled="
                          scope.row.chkState === 0 ? false : 'disabled'
                        "
                        >审核</el-button
                      >
                      <el-button
                        type="text"
                        :disabled="
                          scope.row.publishState === 1 ? false : 'disabled'
                        "
                        >修改</el-button
                      >
                      <el-button
                        type="text"
                        @click="open(scope.row.id, scope.row.publishState)"
                        >{{
                          scope.row.publishState === 1 ? '上架' : '下架'
                        }}</el-button
                      >
                      <el-button
                        type="text"
                        :disabled="
                          scope.row.publishState === 1 ? false : 'disabled'
                        "
                        @click="deleteQuestion(scope.row)"
                        >删除</el-button
                      >
                    </template>
                  </el-table-column>
                </el-table>
              </template></el-tab-pane
            >
            <el-tab-pane label="已审核" name="third"
              ><template>
                <el-table :data="reviewed" style="width: 100%">
                  <el-table-column prop="number" label="试题编号" width="120">
                  </el-table-column>
                  <el-table-column prop="subject" label="学科" width="120">
                  </el-table-column>
                  <el-table-column prop="catalog" label="目录" width="120">
                  </el-table-column>
                  <el-table-column prop="questionType" label="题型" width="120">
                    <template slot-scope="scope">
                      <span v-if="scope.row.questionType === '1'">单选</span>
                      <span v-else-if="scope.row.questionType === '2'"
                        >多选</span
                      >
                      <span v-else>简答</span>
                    </template>
                  </el-table-column>
                  <el-table-column prop="question" label="题干" width="280">
                    <template slot-scope="scope">
                      <div>
                        {{ scope.row.question | textFormat }}
                      </div>
                    </template>
                  </el-table-column>
                  <el-table-column prop="addDate" label="录入时间" width="180">
                    <template slot-scope="scope">
                      <div>
                        {{ scope.row.addDate | dateFormat }}
                      </div>
                    </template>
                  </el-table-column>
                  <el-table-column prop="difficulty" label="难度" width="80">
                    <template slot-scope="scope">
                      <span v-if="scope.row.difficulty === '1'">简单</span>
                      <span v-else-if="scope.row.difficulty === '2'">一般</span>
                      <span v-else>困难</span>
                    </template>
                  </el-table-column>
                  <el-table-column prop="creator" label="录入人" width="120">
                  </el-table-column>
                  <el-table-column prop="chkState" label="审核状态" width="120">
                    <template slot-scope="scope">
                      <span v-if="scope.row.chkState === 0">待审核</span>
                      <span v-else-if="scope.row.chkState === 1">已审核</span>
                      <span v-else>已拒绝</span>
                    </template>
                  </el-table-column>
                  <el-table-column
                    prop="chkRemarks"
                    label="审核意见"
                    width="150"
                  >
                  </el-table-column>
                  <el-table-column prop="chkUser" label="审核人" width="120">
                  </el-table-column>
                  <el-table-column prop="chkState" label="发布状态" width="150">
                    <template slot-scope="scope">
                      <span v-if="scope.row.chkState !== 1">待发布</span>
                      <span v-else-if="scope.row.chkState === 1">已发布</span>
                    </template>
                  </el-table-column>
                  <el-table-column label="操作" fixed="right" width="200">
                    <template class="btnText" slot-scope="scope">
                      <el-button
                        type="text"
                        @click="questionsPreview(scope.row)"
                        >预览</el-button
                      >
                      <el-button
                        type="text"
                        :disabled="
                          scope.row.chkState === 0 ? false : 'disabled'
                        "
                        >审核</el-button
                      >
                      <el-button
                        type="text"
                        :disabled="
                          scope.row.publishState === 1 ? false : 'disabled'
                        "
                        >修改</el-button
                      >
                      <el-button
                        type="text"
                        @click="open(scope.row.id, scope.row.publishState)"
                        >{{
                          scope.row.publishState === 1 ? '上架' : '下架'
                        }}</el-button
                      >
                      <el-button
                        type="text"
                        :disabled="
                          scope.row.publishState === 1 ? false : 'disabled'
                        "
                        @click="deleteQuestion(scope.row)"
                        >删除</el-button
                      >
                    </template>
                  </el-table-column>
                </el-table>
              </template></el-tab-pane
            >
            <el-tab-pane label="已拒绝" name="four"
              ><template>
                <el-table :data="rejected" style="width: 100%">
                  <el-table-column prop="number" label="试题编号" width="120">
                  </el-table-column>
                  <el-table-column prop="subject" label="学科" width="120">
                  </el-table-column>
                  <el-table-column prop="catalog" label="目录" width="120">
                  </el-table-column>
                  <el-table-column prop="questionType" label="题型" width="120">
                    <template slot-scope="scope">
                      <span v-if="scope.row.questionType === '1'">单选</span>
                      <span v-else-if="scope.row.questionType === '2'"
                        >多选</span
                      >
                      <span v-else>简答</span>
                    </template>
                  </el-table-column>
                  <el-table-column prop="question" label="题干" width="280">
                    <template slot-scope="scope">
                      <div>
                        {{ scope.row.question | textFormat }}
                      </div>
                    </template>
                  </el-table-column>
                  <el-table-column prop="addDate" label="录入时间" width="180">
                    <template slot-scope="scope">
                      <div>
                        {{ scope.row.addDate | dateFormat }}
                      </div>
                    </template>
                  </el-table-column>
                  <el-table-column prop="difficulty" label="难度" width="80">
                    <template slot-scope="scope">
                      <span v-if="scope.row.difficulty === '1'">简单</span>
                      <span v-else-if="scope.row.difficulty === '2'">一般</span>
                      <span v-else>困难</span>
                    </template>
                  </el-table-column>
                  <el-table-column prop="creator" label="录入人" width="120">
                  </el-table-column>
                  <el-table-column prop="chkState" label="审核状态" width="120">
                    <template slot-scope="scope">
                      <span v-if="scope.row.chkState === 0">待审核</span>
                      <span v-else-if="scope.row.chkState === 1">已审核</span>
                      <span v-else>已拒绝</span>
                    </template>
                  </el-table-column>
                  <el-table-column
                    prop="chkRemarks"
                    label="审核意见"
                    width="150"
                  >
                  </el-table-column>
                  <el-table-column prop="chkUser" label="审核人" width="120">
                  </el-table-column>
                  <el-table-column prop="chkState" label="发布状态" width="150">
                    <template slot-scope="scope">
                      <span v-if="scope.row.chkState !== 1">待发布</span>
                      <span v-else-if="scope.row.chkState === 1">已发布</span>
                    </template>
                  </el-table-column>
                  <el-table-column label="操作" fixed="right" width="200">
                    <template class="btnText" slot-scope="scope">
                      <el-button
                        type="text"
                        @click="questionsPreview(scope.row)"
                        >预览</el-button
                      >
                      <el-button
                        type="text"
                        :disabled="
                          scope.row.chkState === 0 ? false : 'disabled'
                        "
                        >审核</el-button
                      >
                      <el-button
                        type="text"
                        :disabled="
                          scope.row.publishState === 1 ? false : 'disabled'
                        "
                        >修改</el-button
                      >
                      <el-button
                        type="text"
                        @click="open(scope.row.id, scope.row.publishState)"
                        >{{
                          scope.row.publishState === 1 ? '上架' : '下架'
                        }}</el-button
                      >
                      <el-button
                        type="text"
                        :disabled="
                          scope.row.publishState === 1 ? false : 'disabled'
                        "
                        @click="deleteQuestion(scope.row)"
                        >删除</el-button
                      >
                    </template>
                  </el-table-column>
                </el-table>
              </template></el-tab-pane
            >
          </el-tabs>
        </template>
      </div>
      <!--/数据展示区域 -->
      <!-- 分页 -->
      <div class="page">
       <template>
          <div class="block moveRight">
            <el-pagination
              @size-change="handleSizeChange"
              @current-change="handleCurrentChange"
              :current-page="Number(pagination.page)"
              :page-sizes="[1, 2, 5, 10]"
              :page-size="Number(pagination.pagesize)"
              :total="this.counts"
              layout="prev, pager, next, sizes, jumper"
              background
            >
            </el-pagination>
          </div>
        </template>
        <!-- /分页 -->
      </div>
      <!-- 试题预览对话框 -->
      <question-preview
      :previewDig.sync="dialogVisible"
      :previewMsg="preview"
    ></question-preview>
      <!-- /试题预览对话框 -->
      <!-- 审核对话框 -->
      <el-dialog title="题目审核" :visible.sync="chkDialogVisible" width="30%" style="padding: 20px 20px">
        <el-form ref="formChnk" :model="formChnk" label-width="0px" :rules="rules">
          <el-form-item>
            <el-radio-group v-model="formChnk.resource">
              <el-radio label="1">通过</el-radio>
              <el-radio label="2">拒绝</el-radio>
            </el-radio-group>
          </el-form-item>
          <el-form-item prop="desc" :rules="[
            { required: true, message: '请填写审核意见！', trigger: 'blur' }
          ]">
            <el-input type="textarea" v-model="formChnk.desc" ></el-input>
          </el-form-item>
          <el-form-item style="text-align: center">
            <el-button size="small" @click="chkDialogVisible = false">取消</el-button>
            <el-button type="primary" @click="chengeChnkState(formChnk.resource,formChnk.desc)" size="small">确认</el-button>
          </el-form-item>
        </el-form>
      </el-dialog>
      <!-- /审核对话框 -->
    </el-card>
  </div>
</template>

<script>
import {
  choice,
  choicePublish,
  remove,
  choiceCheck
} from '@/api/hmmm/questions'
import { simple } from '@/api/hmmm/subjects'
import { list } from '@/api/hmmm/directorys'
import { list as tagList } from '@/api/hmmm/tags'
import { simple as userSimple } from '@/api/base/users'
import { parseTime, html2Text } from '../../utils/index'
import questionPreview from '../components/questions-preview.vue'
import { provinces, citys } from '../../api/hmmm/citys'
import loginSocialSigninVue from '../../module-dashboard/components/loginSocialSignin.vue'
export default {
  components: { questionPreview },
  data() {
    return {
      form: {
        subject: '',
        directory: '',
        tags: '',
        question: '',
        questionType: '',
        difficulty: '',
        direction: '',
        creator: '',
        name: '',
        city: '',
        area: ''
      },
      formChnk:{
        resource:'1',
        desc:''
      },
      rules:{
       desc: [{ required: true, message: '请输入审核意见', trigger: 'blur' }]},
      pagination: {
        pagesize: 10,
        page: 1,
      },
      query:{
        pagesize: 10,
        page: 1,
        chkState:0
      },
      counts:0,//总条数
      alertext: '',
      tableData: [], //全部精选题库列表
      toBeReviewed: [], //待审核精选题库列表
      reviewed: [], //已审核精选题库列表
      rejected: [], //已拒绝精选题库列表
      subjectSimple: [], //学科列表
      directorysList: [], //目录列表
      directorys: [], //二级目录列表
      tagsList: [], //标签列表
      tags: [], //二级标签列表
      usersList: [], //用户列表
      cityList: [], //城市列表
      areaList: [], //城市下区列表
      activeName: 'first',
      dialogVisible: false, //是否弹出对话框
      parmas: {}, //参数对象
      preview: {}, //题目预览存错对象
      chkDialogVisible: false,
      state: 0,
    }
  },
  created() {
    this.getChoice(this.pagination)
    this.getSubjectSimple()
    this.getDirectorysList()
    this.getTagList()
    this.getUserList()
    this.cityList = provinces()
  },
  watch: {
    activeName: {
      handler(nv, ov) {
        if (nv === 'second') {
          this.query.chkState = 0
          this.getChoice(this.query)
        } else if (nv === 'third') {
          this.query.chkState = 1
          this.getChoice(this.query)
        } else if (nv === 'four') {
          this.query.chkState = 2
          this.getChoice(this.query)
        } else {
          this.getChoice(this.pagination)
        }
      }
    }
  },
  filters: {
    dateFormat: function(value) {
      return parseTime(value)
    },
    textFormat: function(value) {
      return html2Text(value)
    }
  },
  methods: {
    // 重置表单
    reset(form) {
      this.form = {}
      this.getChoice(this.pagination)
    },
    onSubmit(subject, question) {
      let data = {
        subjectID:subject,
        keyword:question,
        pagesize: 10,
        page: 1,
      }
      this.getChoice(data)
      console.log(subject, question)
    },
    handleClick(tab, event) {
      console.log(tab, event)
    },
    handleSizeChange(val) {
      this.pagination.pagesize = val
      this.getChoice(this.pagination)
    },
    handleCurrentChange(val) {
      this.pagination.page = val
      this.getChoice(this.pagination)
    },
    // 精选题库上下架请求
    async editUpDown(id, publishState) {
      publishState = this.parmas.publishState === 1 ? 0 : 1
      console.log('010' + publishState)
      await choicePublish({
        id: this.parmas.id,
        publishState: publishState
      })
      this.getChoice(this.pagination)
    },
    // 精选题库上下架点击事件
    open(id, publishState) {
      this.parmas = {
        id,
        publishState
      }
      console.log(this.parmas.publishState)
      this.$confirm(
        `您确认${this.parmas.publishState === 0 ? '上架' : '下架'}这道题目吗`,
        '提示',
        {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }
      )
        .then(() => {
          this.editUpDown(id, publishState)
          this.$message({
            type: 'success',
            message: `${publishState === 0 ? '上架' : '下架'}成功`
          })
          console.log('刷新')
        })
        .catch(() => {
          this.$message({
            type: 'info',
            message: `${publishState === 0 ? '上架' : '下架'}取消`
          })
        })
      // this.getChoice()
    },
    // 删除精选题库请求
    async delete(id) {
      console.log(id)
      await remove(id)
      this.getChoice(this.pagination)
    },
    // 删除精选题库点击事件
    deleteQuestion(id) {
      this.$confirm(`此操作将永久删除该题目，是否继续`, '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      })
        .then(() => {
          this.delete(id)
          this.$message({
            type: 'success',
            message: `删除成功`
          })
        })
        .catch(() => {
          this.$message({
            type: 'info',
            message: `取消操作`
          })
        })
    },
    // 精选题库列表请求
    async getChoice(parameters) {
      try {
        const { data } = await choice(parameters)
        if (this.query.chkState === 0) {
          this.toBeReviewed = data.items
        } else if (this.query.chkState=== 1) {
          this.reviewed = data.items
        } else if (this.query.chkState === 2) {
          this.rejected = data.items
        }
        this.tableData = data.items
        this.counts = data.counts
        this.alertext = `数据一共${data.counts}条`
        console.log(data)
      } catch (err) {
        console.log(err)
        console.log('数据获取失败')
      }
    },
    // 题目预览
    questionsPreview(data) {
      this.preview = data
      this.dialogVisible = true
    },
    // 获取学科列表
    async getSubjectSimple() {
      try {
        const { data } = await simple()
        this.subjectSimple = data
        console.log(this.subjectSimple)
      } catch (err) {
        console.log('失败')
      }
    },
    // 获取目录列表
    async getDirectorysList(id) {
      try {
        const { data } = await list({ subjectID: id })

        this.directorysList = data.items
        console.log(this.directorysList)
      } catch (err) {
        console.log('失败')
      }
    },
    // 获取标签列表
    async getTagList() {
      try {
        const { data } = await tagList()

        this.tagsList = data.items

        console.log(this.tagsList)
      } catch (err) {
        console.log('失败')
      }
    },
    //  学科 目录 标签 联动
    choseSubject(subject) {
      this.directorys = this.directorysList.filter(
        subjects => subjects.subjectID === subject
      )
      this.tags = this.tagsList.filter(
        subjects => subjects.subjectID === subject
      )
    },
    changeCity(pname) {
      console.log(123)
      console.log(pname)

      this.areaList = citys(pname)
    },
    async getUserList() {
      try {
        const { data } = await userSimple()

        this.usersList = data

        console.log(this.usersList)
      } catch (err) {
        console.log('失败')
      }
    },
    chkStateChange(id) {
      this.chkDialogVisible = true
      this.state = id
      console.log(this.state)
    },
    async chengeChnkState(resource,desc) {
      console.log(resource,desc)
      await choiceCheck({
        id:this.state,
        chkState: parseInt(resource),
        chkRemarks:desc
      })
      this.getChoice(this.pagination)
      this.chkDialogVisible = false
    }

  }
}
</script>

<style scoped lang="less">
.questionsContainer {
  margin: 10px 0;
  padding: 0px 10px;
  .box-card {
    .top {
      margin-bottom: 13px;
      display: flex;
      justify-content: space-between;
      span {
        font-size: 12px;
        color: pink;
      }
    }
    .select {
      .el-col {
        padding: 0;
      }
      .el-form-item {
        margin-right: 0;
        .el-form-item__lable {
          float: left;
        }
        .el-form-item__content {
          margin-left: 80px;
          height: 32px;
          line-height: 32px;
        }
      }
      .el-form-item--medium {
        margin-bottom: 14px;
      }
      .city {
        .one {
          width: 48%;
          margin-right: 2%;
          .optionText {
            display: inline-block;
            width: 100%;
          }
        }
        .two {
          width: 50%;
        }
      }
    }
    .container {
      .total {
        background-color: #f4f4f5;
        color: #909399;
        margin-bottom: 15px;
      }
      .el-table th,
      .el-table tr {
        background-color: #fff;
      }
      .el-button--text {
        font-size: 12px;
      }
    }
    .page {
      margin-top: 20px;
      text-align: right;
    }
  }
}
</style>
