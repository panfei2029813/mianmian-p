<template>
  <div class="container">
    <!-- 引入 card 样式 -->
    <el-card class="box-card elCard">
      <div slot="header" :class="isShow ? '':'breadGps'">
        <!-- 面包屑导航 -->
        <el-breadcrumb separator-class="el-icon-arrow-right">
          <el-breadcrumb-item>学科管理</el-breadcrumb-item>
          <el-breadcrumb-item>大数据</el-breadcrumb-item>
          <el-breadcrumb-item>目录管理</el-breadcrumb-item>
        </el-breadcrumb>
      </div>
      <div class="text item">
        <!-- 引入 from 标单 -->
        <el-form
          :inline="true"
          :model="requestParameters"
          class="demo-form-inline"
          size="small"
          style="padding-top: 10px"
        >
          <el-form-item label="目录名称" style="margin-left: 12px">
            <el-input v-model="requestParameters.directoryName"></el-input>
          </el-form-item>
          <el-form-item label="状态" label-width="80px">
            <el-select v-model="requestParameters.state" placeholder="请选择">
              <el-option label="已启用" value= 1></el-option>
              <el-option label="已禁用" value= 0></el-option>
            </el-select>
          </el-form-item>
          <el-form-item>
            <el-button @click="clearAll">清除</el-button>
            <el-button type="primary" @click="onSearch">搜索</el-button>
          </el-form-item>
          <div class="rightBtn">
            <el-button
              type="text"
              icon="el-icon-back"
              size="medium"
              :class="isShow ? '':'breadGps'"
              @click="$router.back()"
            >
              返回学科</el-button
            >
            <el-button
              type="success"
              icon="el-icon-edit"
              size="small"
              @click="addDialogVisible = true"
              >新增目录</el-button
            >
          </div>
        </el-form>
        <!-- 灰色长条区域 -->
        <div class="longBox">
          <i class="el-icon-info iconList"></i>
          <span style="font-size: 13px" class="moveList">{{
            `数据一共${this.total}条`
          }}</span>
        </div>
        <!-- 引入 table 区域 -->
        <template>
          <el-table :data="dataList" style="width: 100%">
            <el-table-column type="index" label="序号" width="80px">
            </el-table-column>
            <el-table-column label="所属学科" width="153px">
              <template slot-scope="scope">
                <div>
                  {{ scope.row.subjectName }}
                </div>
              </template>
            </el-table-column>
            <el-table-column label="目录名称" width="152px">
              <template slot-scope="scope">
                <div>
                  {{ scope.row.directoryName }}
                </div>
              </template>
            </el-table-column>
            <el-table-column label="创建者" width="152px">
              <template slot-scope="scope">
                <div>
                  {{ scope.row.username }}
                </div>
              </template>
            </el-table-column>
            <el-table-column label="创建日期" width="152px">
              <template slot-scope="scope">
                <div>
                  {{ scope.row.addDate | dateFormat }}
                </div>
              </template>
            </el-table-column>
            <el-table-column label="面试题数量" width="150px">
              <template slot-scope="scope">
                <div>
                  {{ scope.row.totals }}
                </div>
              </template>
            </el-table-column>
            <el-table-column prop="state" label="状态" width="150px">
              <template slot-scope="scope">
                <span v-if="scope.row.state === 1">已启用</span>
                <span v-else>已禁用</span>
              </template>
            </el-table-column>
            <el-table-column label="操作" width="150px">
              <template slot-scope="scope">
                <el-button type="text" @click="handleStatus(scope.row)">
                  <span v-if="scope.row.state == 0">启用</span>
                  <span v-else>禁用</span>
                </el-button>
                <el-button
                  size="medium"
                  @click="handleChange(scope.row)"
                  type="text"
                  :disabled="scope.row.state === 0 ? 'disabled' : false"
                  >修改
                </el-button>
                <el-button
                  v-if="scope.row.status != 'deleted'"
                  type="text"
                  @click="removeUser(scope.row.id)"
                  :disabled="scope.row.state === 0 ? 'disabled' : false"
                  >删除
                </el-button>
              </template>
            </el-table-column>
          </el-table>
        </template>
        <!-- 翻页区域 -->
        <template>
          <div class="block moveRight">
            <el-pagination
              @size-change="handleSizeChange"
              @current-change="handleCurrentChange"
              :current-page="Number(pagination.page)"
              :page-sizes="[5, 10, 20, 50]"
              :page-size="Number(pagination.pagesize)"
              :total="this.total"
              layout="prev, pager, next, sizes, jumper"
              background
            >
            </el-pagination>
          </div>
        </template>
      </div>
    </el-card>
    <!-- 修改目录对话框 -->
    <el-dialog title="修改目录" :visible.sync="changeDialogVisible" width="30%">
      <el-form ref="formChange" :model="formChange" label-width="20%">
        <el-form-item
          label="目录名称"
          size="small"
          prop="name"
          :rules="[
            { required: true, message: '请输入目录名称', trigger: 'blur' }
          ]"
        >
          <el-input v-model="formChange.name" class="dialogInp"></el-input>
        </el-form-item>
        <el-form-item class="dialogBtn">
          <el-button size="medium" @click="changeDialogVisible = false"
            >取消</el-button
          >
          <el-button type="primary" @click="onChenge" size="medium"
            >确认</el-button
          >
        </el-form-item>
      </el-form>
    </el-dialog>
    <!-- /修改目录对话框 -->
    <!-- 新增目录对话框 -->
    <el-dialog title="新增目录" :visible.sync="addDialogVisible" width="30%">
      <el-form ref="formAdd" :model="formAdd" label-width="20%">
        <el-form-item label="所属学科" prop="subject">
          <el-select v-model="formAdd.subjectID" placeholder="请选择" size="small">
            <el-option
              v-for="(item, index) in subjectSimple"
              :key="index"
              :label="item.label"
              :value="item.value"
            ></el-option>
          </el-select>
        </el-form-item>
        <el-form-item
          label="目录名称"
          size="small"
          prop="name"
          :rules="[
            { required: true, message: '请输入目录名称', trigger: 'blur' }
          ]"
        >
          <el-input v-model="formAdd.name" class="dialogInp"></el-input>
        </el-form-item>
        <el-form-item class="dialogBtn">
          <el-button size="medium" @click="addDialogVisible = false"
            >取消</el-button
          >
          <el-button type="primary" @click="onAdd" size="medium"
            >确认</el-button
          >
        </el-form-item>
      </el-form>
    </el-dialog>
    <!-- /新增目录对话框 -->
  </div>
</template>

<script>
import { parseTime, html2Text } from '../../utils/index'
import { simple } from '@/api/hmmm/subjects'
import { list, remove, changeState, update, add } from '@/api/hmmm/directorys'
export default {
  data() {
    return {
      subjectSimple: [], //学科列表
      dataList: [],
      tableKey: 0,
      total: null,
      parms: {},
      isShow:false,
      changeDialogVisible: false,
      addDialogVisible: false,
      formChange: {
        name: ''
      },
      formAdd: {
        name: '',
        subjectID:null
      },
      pagination:{
        page: 1,
        pagesize: 10,
      },
      subject:{
        page: 1,
        pagesize: 10,
        subjectID:null
      },
      requestParameters: {
        page: 1,
        pagesize: 10,
        directoryName:'',
        state:null
      }
    }
  },
  created() {
    
    if(this.$route.query.id){
      this.subject.subjectID = this.$route.query.id
      this.isShow = true
      this.getList(this.subject)
    }else{
    this.getList(this.pagination)
    }
    this.getSubjectSimple()
  },
  filters: {
    dateFormat: function(value) {
      return parseTime(value)
    }
  },
  methods: {
    clearAll() {
     this.requestParameters = {}
     this.getList(this.pagination)
    },
    onSearch() {
       this.getList(this.requestParameters)
      console.log(this.requestParameters)
    },
    addNewCatalog() {
      console.log(456)
    },
    async getList(parameters) {
      try {
        const { data } = await list(parameters)
        console.log(data)
        this.dataList = data.items
        this.total = data.counts
      } catch (err) {
        console.log('error')
      }
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
    // 每页显示信息条数
    handleSizeChange(val) {
      this.pagination.pagesize = val
       this.getList(this.pagination)
    },
    // 进入某一页
    handleCurrentChange(val) {
      this.pagination.page = val
       this.getList(this.pagination)
    },
    // 删除
    removeUser(id) {
      console.log(id)
      this.$confirm('此操作将永久删除该文章, 是否继续?', '提示', {
        type: 'warning'
      })
        .then(async () => {
          await remove({ id: id })
            .then(response => {
              this.$message.success('成功删除了用户' + '!')
              this.getList(this.requestParameters)
            })
            .catch(response => {
              this.$message.error('删除失败!')
            })
        })
        .catch(() => {
          this.$message.info('已取消操作!')
        })
    },
    //启用，禁用
   async handleStatus(val) {
      var status = ''
      if (val.state === 1) {
        val.state = 0
        status = '禁用'
      } else {
        val.state = 1
        status = '启用'
      }
      // let state = !val.state
      var data = {
        id: val.id,
        state: val.state
      }
      try{
        await changeState(data)
        this.$message({
            type: 'success',
            message: `已${status}`
          })
        }catch(err){
          this.$message({
            type: 'info',
            message: `已${status}`
          })
      }
      this.getList()
            
    },
    // 修改目录
    async onChenge() {
      this.changeDialogVisible = false
      try {
        await update({
          id: this.parms.id,
          directoryName: this.formChange.name,
          subjectID: this.parms.subjectID
        })
        this.$message({
          type: 'success',
          message: '修改成功'
        })
      } catch (err) {
        this.$message({
          type: 'info',
          message: '修改失败'
        })
        console.log(err)
      }
      this.getList()
    },
    handleChange(name) {
      this.changeDialogVisible = true
      this.parms = name
      this.formChange.name = this.parms.directoryName
    },
    // 新增目录
    async onAdd() {
      this.addDialogVisible = false
      console.log( this.formAdd.subjectID)
      try {
        await add({
          directoryName: this.formAdd.name,
          subjectID: this.formAdd.subjectID
        })
        this.$message({
          type: 'success',
          message: '添加成功'
        })
      } catch (err) {
        this.$message({
          type: 'info',
          message: '添加失败'
        })
        console.log(err)
      }
      this.getList()
    }
  }
}
</script>

<style scoped lang="less">
.elCard {
  margin: 10px;
  .breadGps {
    display: none;
  }
  .rightBtn {
    float: right;
  }
  .longBox {
    background-color: #f4f4f5;
    color: #909399;
    padding: 8px 16px;
    margin-bottom: 15px;
    .iconList {
      font-size: 16px;
    }
    .moveList {
      padding: 0 8px;
    }
  }
  .moveRight {
    float: right;
    margin: 20px 0;
  }
}
/deep/.el-dialog__body {
  padding: 30px 10px 10px;
  .dialogInp {
    width: 100%;
    height: 32px;
    line-height: 32px;
    margin-bottom: 40px;
  }
  .dialogBtn {
    text-align: right;
  }
}
</style>
