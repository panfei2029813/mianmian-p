<template>
  <div class="container">
    <!-- 卡片视图区域 -->
    <el-card class="card">
      <el-breadcrumb v-show="requestParameters.subjectID" separator-class="el-icon-arrow-right">
        <el-breadcrumb-item>学科管理</el-breadcrumb-item>
        <el-breadcrumb-item>{{ subjectRow.subjectName }}</el-breadcrumb-item>
        <el-breadcrumb-item>标签管理</el-breadcrumb-item>
      </el-breadcrumb>

      <!-- <搜索> -->
      <el-row class="fromAndEdit">
        <el-form :model="requestParameters" ref="requestParameters"  class="demo-form-inline">
          <div class="filter-container">
            <el-row :gutter="20">
              <el-col :span="5">
                <el-form-item label="标签名称" prop="tagName">
                  <el-input
                    @keyup.enter="handleFilter"
                    style="width: 180px;"
                    class="filter-item"
                    v-model="requestParameters.tagName"
                  ></el-input>
                </el-form-item>
              </el-col>
              <el-col :span="5">
                <el-form-item label="状态" prop="state">
                  <el-select
                    class="filter-item"
                    style="width: 150px;"
                    v-model="requestParameters.state"
                    @keyup.enter="handleFilter"
                  >
                    <el-option
                      v-for="item in status"
                      :key="item.value"
                      :label="item.label"
                      :value="item.value"
                    ></el-option>
                  </el-select>
                </el-form-item>
              </el-col>
              <el-col :span="2">
                <el-form-item>
                  <el-button style="margin-left:-60px" size="small" @click="handleClear">清除</el-button>
                </el-form-item>
              </el-col>
              <el-col :span="2">
                <el-form-item>
                  <el-button
                    style="margin-left:-90px"
                    type="primary"
                    size="small"
                    @click="handleFilter"
                  >搜索</el-button>
                </el-form-item>
              </el-col>

              <div class="fromAdd">
                <el-button
                  v-show="requestParameters.subjectID"
                  type="text"
                  icon="el-icon-arrow-left"
                  class="textIcon"
                  @click="goBack"
                >返回学科</el-button>
                <el-button
                style="height:35px"
                  type="success"
                  class="filter-item fr"
                  @click="addNewTag"
                  icon="el-icon-edit"
                  size="small"
                >新增目录</el-button>
              </div>
            </el-row>
          </div>
        </el-form>
      </el-row>
      <!-- 数据记录 -->
      <el-alert
        v-if="alertText !== ''"
        :title="alertText"
        type="info"
        style=" margin-bottom: 15px;"
        :closable="false"
        show-icon
      ></el-alert>
      <!-- 数据列表 -->
      <el-table
        :data="dataList"
        v-loading="listLoading"
        element-loading-text="给我一点时间"
        fit
        highlight-current-row
        style="width: 100%"
        border
      >
        <el-table-column align="center" label="序号" type="index" width="50px">
          <!-- <template slot-scope="scope">
            <span>{{scope.row.id}}</span>
          </template>-->
        </el-table-column>
        <el-table-column align="center" label="所属学科">
          <template slot-scope="scope">
            <span>{{scope.row.subjectName}}</span>
          </template>
        </el-table-column>
        <el-table-column align="center" label="标签名称">
          <template slot-scope="scope">
            <span>{{scope.row.tagName}}</span>
          </template>
        </el-table-column>
        <el-table-column align="center" label="创建者">
          <template slot-scope="scope">
            <span>{{scope.row.username}}</span>
          </template>
        </el-table-column>
        <el-table-column align="center" label="创建日期">
          <template slot-scope="scope">
            <span>{{scope.row.addDate | parseTime()}}</span>
          </template>
        </el-table-column>
        <el-table-column align="center" label="状态">
          <template slot-scope="scope">
            <span v-if="scope.row.state==1">已启用</span>
            <span v-else>已禁用</span>
          </template>
        </el-table-column>
        <el-table-column
          align="center"
          label="操作"
          width="280"
          class-name="small-padding fixed-width"
        >
          <template slot-scope="scope">
            <el-button class="handleStatusBtn" size="mini" @click="handleStatus(scope.row)">
              <span v-if="scope.row.state==0">启用</span>
              <span v-else>禁用</span>
            </el-button>
            <el-button
              :disabled="scope.row.state === 0?false:true"
              class="handleStatusBtn"
              size="mini"
              @click="editTag(scope.row)"
            >修改</el-button>
            <el-button
              :disabled="scope.row.state === 0?false:true"
              class="handleStatusBtn"
              v-if="scope.row.status!='deleted'"
              size="mini"
              @click="removeTag(scope.row)"
            >删除</el-button>
          </template>
        </el-table-column>
      </el-table>
      <!-- 分页 -->
      <el-pagination
        background
        @size-change="handleSizeChange"
        @current-change="handleCurrentChange"
        :current-page="Number(requestParameters.page)"
        :total="Number(total)"
        :page-size="Number(requestParameters.pagesize)"
        :page-sizes="[3,5,10,20]"
        layout="sizes, prev, pager, next, jumper"
      ></el-pagination>
    </el-card>
    <!-- ----------------------- -->
    <!-- 新增标签弹出层 -->
    <el-dialog
      :title="dialogTitleText"
      :visible.sync="addDialogVisible"
      width="50%"
      @close="addDialogClose"
    >
      <!-- 添加标签表单 -->
      <el-form
        :model="addForm"
        :rules="formRules"
        ref="addFormRefs"
        label-position="left"
        label-width="100px"
      >
        <el-form-item label="所属学科">
          <el-select
            class="filter-item"
            style="width: 130px;"
            v-model="addForm.subjectID"
            @keyup.enter="handleFilter"
            filterable
          >
            <el-option
              v-for="item in subjectSelect.data"
              :key="item.value"
              :label="item.label"
              :value="item.value"
            ></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="标签名称" prop="tagName">
          <el-input v-model="addForm.tagName"></el-input>
        </el-form-item>
      </el-form>
      <!-- 底部 -->
      <div slot="footer" class="dialog-footer">
        <el-button @click="cancleAddDialog">取消</el-button>
        <el-button type="primary" @click="sureTag">确定</el-button>
      </div>
    </el-dialog>
  </div>
</template>

<script>
import { parseTime } from '@/utils/index.js'
import { status } from '@/api/hmmm/constants'
import { simple } from '@/api/hmmm/subjects.js'
import { list, changeState, remove, add, update, detail } from '@/api/hmmm/tags'
export default {
  name: 'tagsList',
  components: {

  },
  data () {
    return {
      // 学科传递数据
      subjectRow: {},
      dataList: [],
      total: null,
      listLoading: false,
      // 数据条数
      alertText: '',
      // 获取列表参数
      requestParameters: {
        page: 1,
        pagesize: 10
      },
      // 下拉数据
      subjectSelect: '',
      // 新增标签弹出层内容
      addForm: {
        tagName: '',
        subjectID: ''
      },
      // 表单验证
      formRules: {
        tagName: [
          { required: true, message: '请输入标签名称', trigger: 'blur' },
          { min: 2, max: 7, message: '长度在 2 到 7 个字符', trigger: 'blur' }
        ]
      },
      // 新增学科弹框显示隐藏
      addDialogVisible: false,
      // 添加修改弹框title文本
      dialogTitleText: '',
      // 修改标签的id
      editTagID: null,
      // 修改前标签名称
      oldTagName: ''
    }
  },
  computed: {
    status () {
      return status
    }
  },
  filters: {
    parseTime
  },
  created () {
    this.subjectRow = this.$route.query
    this.getSubjectData()
    this.initialDate()
  },
  mounted () {

  },
  methods: {
    // 初始数据
    initialDate () {
      // 渲染列表
      this.getList()
    },
    // 获取列表数据
    async getList (params) {
      try {
        if (this.subjectRow) {
          this.requestParameters.subjectID = this.subjectRow.id
        } else {
          this.requestParameters.subjectID = null
        }
        this.listLoading = true
        const { data: res } = await list(this.requestParameters)
        this.dataList = res.items
        this.total = res.counts
        this.alertText = `共${this.total}条记录`
        this.listLoading = false
      } catch (err) {
        this.$message.error('获取数据失败')
      }
      // console.log(this)
    },
    // 清除
    handleClear () {
      this.$refs.requestParameters.resetFields()
      this.getList()
    },
    // 查询
    handleFilter () {
      this.requestParameters.page = 1
      this.getList(this.requestParameters)
    },
    // 页码跳转
    handleCurrentChange (val) {
      this.requestParameters.page = val
      this.getList()
    },
    // 每页显示信息条数
    handleSizeChange (val) {
      this.requestParameters.pagesize = val
      this.getList(this.requestParameters)
    },
    // 点击启用禁用按钮
    handleStatus (val) {
      var status = ''
      if (val.state === 1) {
        val.state = 0
        status = '禁用'
        // 修改删除按钮禁用
      } else {
        val.state = 1
        status = '启用'
      }
      var data = {
        id: val.id,
        state: val.state
      }
      this.$confirm('已成功' + status + ', 是否继续?', '提示', { type: 'warning' })
        .then(async () => {
          await changeState(data)
            .then(response => {
              this.$message.success('已成功' + status)
              this.getList(this.requestParameters)
              console.log(this.requestParameters)
            })
            .catch(response => {
              this.$message.error(status + '失败')
            })
        })
        .catch(() => {
          this.$message.info('已取消操作')
        })
    },
    // 获取学科
    async getSubjectData () {
      this.subjectSelect = await simple()
      // console.log(this.subjectSelect)
    },
    // 点击新增标签按钮
    addNewTag () {
      this.addDialogVisible = true
      this.dialogTitleText = '新增标签'
    },
    // 对话框中点击取消按钮
    cancleAddDialog () {
      this.addDialogVisible = false
      this.$message.info('已取消')
    },
    // 点击确定添加标签
    sureTag () {
      // 判断用户是添加还是修改
      if (this.dialogTitleText === '新增标签') {
        // 添加
        // 表单验证
        this.$refs.addFormRefs.validate(valid => {
          if (!valid) {
            return this.$message.error('请输入正确的的标签名称')
          } else {
            this.addTagFun()
          }
        })
      } else if (this.dialogTitleText === '修改标签') {
        // 调用函数，修改标签
        // 表单验证
        this.$refs.addFormRefs.validate(valid => {
          if (!valid) {
            return this.$message.error('请输入正确的标签名称')
          } else {
            this.editTagFun()
          }
        })
      }
    },
    // 添加标签函数
    async addTagFun () {
      var requestBody = this.addForm
      // 发起请求
      try {
        await add({
          subjectID: requestBody.subjectID,
          tagName: requestBody.tagName
        })
        // 成功,渲染页面
        this.initialDate()
        // 提示用户
        this.$message.success('操作成功')
      } catch (err) {
        // console.log(err)
        this.$message.error('操作失败，请重试')
      }
      this.addDialogVisible = false
    },
    // 修改标签函数
    async editTagFun () {
      // 判断
      if (this.addForm.tagName === this.oldTagName) {
        this.addDialogVisible = false
        return this.$message.warning('未做任何修改')
      }
      // 根据id修改标签
      try {
        // console.log(this.addForm);
        await update({ id: this.editTagID, subjectID: this.addForm.subjectID, tagName: this.addForm.tagName })
        // 成功
        this.initialDate()
        this.$message.success('修改标签成功')
      } catch (err) {
        this.$message.error('修改标签失败，请稍后重试')
      }
      this.addDialogVisible = false
    },
    // 对话框关闭事件
    addDialogClose () {
      // 重置表单及校验规则
      this.$refs.addFormRefs.resetFields()
    },
    // 点击修改按钮
    async editTag (row) {
      console.log(row)
      // 根据ID获取标签详情 里面的subjectID
      const data = await detail({ id: row.id })
      console.log(data)
      // console.log(row);
      this.oldTagName = row.tagName
      this.editTagID = row.id
      this.dialogTitleText = '修改标签'
      this.addDialogVisible = true
      // 数据回显
      this.addForm.tagName = row.tagName
      this.addForm.subjectID = data.data.subjectID
    },
    // 删除
    removeTag (row) {
      this.$confirm('此操作将永久删除标签 ' + ', 是否继续?', '提示', {
        type: 'warning'
      })
        .then(async () => {
          await remove({ id: row.id })
            .then(response => {
              this.$message.success('成功删除标签')
              this.dataList.splice(row, 1)
              this.getList(this.requestParameters)
            })
            .catch(response => {
              this.$message.error('删除失败')
            })
        })
        .catch(() => {
          this.$message.info('已取消操作')
        })
    },
    // 返回学科
    goBack () {
      this.$router.go(-1)
    }
  }
}
</script>

<style scoped lang='less'>
.container {
  padding: 20px 15px;
  .el-breadcrumb {
    margin-bottom: 20px;
  }
  .handleStatusBtn {
    border: none;
    background-color: #fff;
  }
  .el-pagination {
    margin: 15px 5px;
    display: flex;
    justify-content: flex-end;
  }
  .fromAndEdit {
    position: relative;
    .fromAdd {
      width: 110px;
      height: 35px;
      display: flex;
      justify-content: flex-end;
      position: absolute;
      right: 0;
      top: 0;
      .textIcon {
        font-size: 16px;
      }
    }
  }
}
</style>
