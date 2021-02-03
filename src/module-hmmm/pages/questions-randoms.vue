<template>
  <div class="container">
    <el-card class="box-card">
      <!-- 头部 -->
      <el-form ref="form" :model="form" label-width="80px">
        <!-- 头部 -->
        <el-row :gutter="20">
          <!-- 输入框 -->
          <el-col :span="8"
            ><el-form-item label="关键字" class="input">
              <el-input
                placeholder="根据关键字搜索"
                v-model="form.name"
              ></el-input> </el-form-item
          ></el-col>
          <!-- 输入框 -->
          <!-- 按钮 -->
          <el-col :offset="13" :span="3" style="text-align:right">
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
        <el-table-column prop="id" label="编号"> </el-table-column>
        <el-table-column prop="questionType" label="题型" width="80">
          <template slot-scope="scope">
            <div v-if="scope.row.questionType == 1">单选</div>
            <div v-else-if="scope.row.questionType == 2">多选</div>
            <div v-else>填空</div>
          </template>
        </el-table-column>
        <el-table-column prop="questionIDs" label="题目编号">
          <template slot-scope="scope">
            <div @click="onPageShow(scope.row)">
              <div
                class="questionIDs"
                v-for="(item, index) in scope.row.questionIDs"
                :key="index"
              >
                {{ item.number }}
              </div>
            </div>
          </template>
        </el-table-column>
        <el-table-column prop="addTime" label="录入时间"> </el-table-column>
        <el-table-column prop="totalSeconds" label="答题时间">
        </el-table-column>
        <el-table-column prop="accuracyRate" label="正确率"> </el-table-column>
        <el-table-column prop="userName" label="录入人"> </el-table-column>
        <el-table-column prop="address" label="操作" width="60px">
          <template slot-scope="scope">
            <el-button
              type="danger"
              plain
              icon="el-icon-delete"
              circle
              @click="onRemoveRandoms(scope.row)"
            ></el-button>
          </template>
        </el-table-column>
      </el-table>
      <!-- 表格 -->
      <questions-preview
        title="收货地址"
        :previewMsg="previewMsg"
        :previewDig.sync="dialogTableVisible"
      >
      </questions-preview>
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
    </el-card>
  </div>
</template>

<script>
import { randoms, removeRandoms } from '@/api/hmmm/questions'
import questionsPreview from '../components/questions-preview'
export default {
  data() {
    return {
      form: {
        name: ''
      },
      query: {
        page: 1,
        pagesize: 10,
        keyword: ''
      },
      total: 0,
      tableData: [],
      dialogTableVisible: false,
      previewMsg: {}
    }
  },
  components: {
    questionsPreview
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
        const { data } = await randoms(this.query)
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
        await removeRandoms(row)
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
      this.query.keyword = this.form.name
      this.loadRandoms()
      this.form.name = ''
    },
    onPageShow(el) {
      console.log(el, 'el')
      this.previewMsg = el
      this.dialogTableVisible = true
    }
  }
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
