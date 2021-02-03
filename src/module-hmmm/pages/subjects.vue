<template>
  <div class="container">
    <el-card class="box-card">
      <!-- 头部 -->
      <el-form ref="form" :model="form" label-width="120px">
        <!-- 头部 -->
        <el-row :gutter="20">
          <!-- 输入框 -->
          <el-col :span="5"
            ><el-form-item label="学科名称：" class="input">
              <el-input
                placeholder="根据学科名称搜索"
                v-model="form.name"
              ></el-input> </el-form-item
          ></el-col>
          <!-- 输入框 -->
          <!-- 按钮 -->
          <el-col :span="3" style="text-align:right">
            <el-row class="header-button">
              <el-button @click="clearInput">清除</el-button>
              <el-button type="primary" @click="onSearch">搜索</el-button>
            </el-row>
          </el-col>
          <!-- 按钮 -->
        </el-row>
        <!-- 头部 -->
      </el-form>
      <!-- 头部 -->
      <el-row>
        <el-col :span="24"
          ><div class="grid-content-total bg-purple-dark">
            <i class="el-icon-info"></i>
            数据一共 {{ total }} 条
          </div></el-col
        >
      </el-row>
      <!-- 表格 -->
      <el-table :data="tableData" style="width: 100%" ref="removeId">
        <el-table-column prop="id" label="编号" width="80"> </el-table-column>
        <el-table-column prop="subjectName" label="学科名称"> </el-table-column>
        <el-table-column prop="username" label="创建者"> </el-table-column>
        <el-table-column prop="addDate" label="录入时间">
          <template slot-scope="scope">
            {{ scope.row.addDate | parseTime }}
          </template>
        </el-table-column>
        <el-table-column prop="isFrontDisplay" label="是否前台显示">
          <template slot-scope="scope">
            {{ scope.row.isFrontDisplay == 1 ? '是' : '否' }}
          </template>
        </el-table-column>
        <el-table-column prop="twoLevelDirectory" label="二级目录">
        </el-table-column>
        <el-table-column prop="tags" label="标签"> </el-table-column>
        <el-table-column prop="address" label="操作" width="260px">
          <template slot-scope="scope" class="change">
            <el-button type="text" size="small" @click="onClassify(scope.row)"
              >学科分类</el-button
            >
            <el-button type="text" size="small" @click="onTag(scope.row)"
              >学科标签
            </el-button>
            <el-button type="text" size="small" @click="onEdit(scope.row)"
              >修改</el-button
            >
            <el-button type="text" size="small" @click="onRemove(scope.row)"
              >删除</el-button
            >
          </template>
        </el-table-column>
      </el-table>
      <!-- 表格 -->

      <!-- 分页 -->
      <div class="block">
        <el-pagination
          @size-change="handleSizeChange"
          @current-change="handleCurrentChange"
          :current-page="query.page"
          :page-sizes="[10, 20, 50, 100]"
          :page-size="10"
          layout="  prev, pager, next, sizes,jumper"
          :total="total"
        >
        </el-pagination>
      </div>
      <!-- 分页 -->
      <el-dialog title="收货地址" :visible.sync="dialogFormVisible" width="30%">
        <el-form
          :model="editForm"
          ref="editForm"
          label-width="100px"
          :rules="rules"
        >
          <el-form-item label="活动名称" prop="subjectName">
            <el-input v-model="editForm.subjectName"></el-input>
          </el-form-item>

          <el-form-item label="是否显示" prop="isFrontDisplay">
            <el-switch
              style="display: block"
              v-model="editForm.isFrontDisplay"
              active-color="#13ce66"
              inactive-color="#ff4949"
              :width="60"
            >
            </el-switch>
          </el-form-item>
        </el-form>
        <div slot="footer" class="dialog-footer">
          <el-button @click="dialogFormVisible = false">取 消</el-button>
          <el-button type="primary" @click="onEditDig">确 定</el-button>
        </div>
      </el-dialog>
    </el-card>
  </div>
</template>

<script>
import { list, update, remove } from '@/api/hmmm/subjects'
import { parseTime } from '@/utils/index.js'
export default {
  data() {
    return {
      form: {
        name: ''
      },
      query: {
        page: 1,
        pagesize: 10,
        subjectName: ''
      },
      editForm: { subjectName: '', isFrontDisplay: 0, id: 0 },
      rules: {
        subjectName: [
          { required: true, message: '请输入活动名称', trigger: 'blur' }
        ]
      },
      total: 0,
      tableData: [],
      dialogFormVisible: false
    }
  },
  created() {
    this.loadRandoms()
  },
  methods: {
    clearInput() {
      this.form.name = ''
    },
    async loadRandoms() {
      console.log(this.query)
      try {
        const { data } = await list(this.query)
        console.log(data)
        this.total = data.counts
        this.tableData = data.items
      } catch (error) {
        this.$message({
          message: '亲！网页走丢了奥',
          type: 'warning'
        })
      }
    },

    async onRemoveRandoms(row) {
      try {
        this.loadRandoms()
        this.$message({
          message: '恭喜你，删除成功了',
          type: 'success'
        })
      } catch (error) {
        this.$message({
          message: '亲！删除失败了',
          type: 'warning'
        })
      }
    },
    handleSizeChange(el) {
      this.query.pagesize = el
      this.loadRandoms()
    },
    handleCurrentChange(el) {
      this.query.page = el
      this.loadRandoms()
    },
    onSearch() {
      this.query.subjectName = this.form.name
      this.loadRandoms()
      this.form.name = ''
    },
    onClassify(el) {
      this.$router.push({
        name: 'subjects-directorys',
        query: {
          id: el.id,
          name: el.subjectName
        }
      })
    },
    onTag(el) {
      this.$router.push({
        name: 'subjects-tags',
        query: {
          id: el.id,
          name: el.subjectName
        }
      })
    },
    onEdit(el) {
      console.log(el)
      this.dialogFormVisible = true
      this.editForm.id = el.id
      this.editForm.subjectName = el.subjectName
      this.editForm.isFrontDisplay = Boolean(el.isFrontDisplay)
    },
    onRemove(el) {
      this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      })
        .then(async () => {
          await remove(el)
          this.loadRandoms()
          this.$message({
            type: 'success',
            message: '删除成功!'
          })
        })
        .catch(() => {
          this.$message({
            type: 'info',
            message: '已取消删除'
          })
        })
    },
    async onEditDig() {
      this.editForm.isFrontDisplay = Number(this.editForm.isFrontDisplay)
      console.log(this.editForm)
      await update(this.editForm)
      this.dialogFormVisible = false
      this.loadRandoms()
      this.$message({
        showClose: true,
        message: '啦啦啦啦  ',
        type: 'success'
      })
    }
  },
  filters: { parseTime }
}
</script>

<style scoped lang="less">
.container {
  padding: 20px 10px 50px;
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
  .questionIDs {
    color: blue;
    cursor: pointer;
  }
}
</style>
