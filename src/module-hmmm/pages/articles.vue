<template>
  <div class="container">
    <!-- 卡片主体区域 -->
    <el-card class="box-card">
      <!-- 头部 -->
      <el-form ref="form" :model="form" label-width="80px">
        <el-row :gutter="20">
          <!-- 输入框 -->
          <el-col :span="6"
            ><el-form-item label="关键字" class="input">
              <el-input
                placeholder="根据关键字搜索"
                v-model="form.name"
                @change="onInput"
              ></el-input> </el-form-item
          ></el-col>
          <!-- 输入框 -->

          <!-- 下拉菜单 -->
          <el-col :span="6"
            ><el-form-item label="状态" class="input">
              <el-select
                v-model="value"
                placeholder="请选择"
                @change="selectChange"
              >
                <el-option
                  v-for="item in options"
                  :key="item.value"
                  :label="item.label"
                  :value="item.value"
                >
                </el-option>
              </el-select> </el-form-item
          ></el-col>
          <!-- 下拉菜单 -->

          <!-- 搜索清除按钮 -->
          <el-col :span="3" style="text-align:right">
            <el-row class="header-button">
              <el-button @click="clearInput">清除</el-button>
              <el-button type="primary" @click="onSearch">搜索</el-button>
            </el-row>
          </el-col>
          <!-- 搜索清除按钮 -->

          <!-- 新增技巧按钮 -->
          <el-col :offset="6" :span="3" style="text-align:right">
            <el-row class="header-button">
              <el-button
                type="success"
                icon="el-icon-edit"
                @click="onIsArticlesAddshow"
              >
                新增技巧
              </el-button>
            </el-row>
          </el-col>
          <!-- 新增技巧按钮 -->
        </el-row>
      </el-form>
      <!-- 头部 -->

      <!-- 数据总数 -->
      <el-row>
        <el-col :span="24"
          ><div class="grid-content-total bg-purple-dark">
            <i class="el-icon-info"></i>
            数据一共 {{ total }} 条
          </div></el-col
        >
      </el-row>
      <!-- 数据总数 -->

      <!-- 表格 -->
      <el-table :data="tableData" style="width: 100%" ref="removeId">
        <!-- 序号行 -->
        <el-table-column type="index" width="50" label="序号">
        </el-table-column>
        <!-- 序号行 -->
        <!-- 标题行 -->
        <el-table-column prop="title" label="文章标题">
          <template slot-scope="scope">
            <span>{{ scope.row.title }}</span>
            <span
              v-show="Boolean(scope.row.videoURL)"
              class="el-icon-film filmSpan"
              @click="onVideoShow(scope.row.videoURL)"
            ></span>
          </template>
        </el-table-column>
        <!-- 标题行 -->
        <!-- 阅读数行 -->
        <el-table-column prop="visits" label="阅读数"> </el-table-column>
        <!-- 阅读数行 -->
        <!-- 录入人行 -->
        <el-table-column prop="username" label="录入人"> </el-table-column>
        <!-- 录入人行 -->
        <!-- 状态行 -->
        <el-table-column prop="state" label="状态">
          <template slot-scope="scope"
            >{{ scope.row.state ? '已启用' : '已禁用' }}
          </template>
        </el-table-column>
        <!-- 状态行 -->
        <!-- 操作行 -->
        <el-table-column prop="address" label="操作" width="220px">
          <template slot-scope="scope" class="change">
            <el-button type="text" size="small" @click="onPreview(scope.row)"
              >浏览</el-button
            >
            <el-button type="text" size="small" @click="onDisabled(scope.row)"
              >{{ scope.row.state ? '禁用' : '启用' }}
            </el-button>
            <el-button
              type="text"
              size="small"
              :disabled="!scope.row.state"
              @click="onEditArticle(scope.row)"
              >修改</el-button
            >
            <el-button
              type="text"
              size="small"
              @click="onRemoveRandoms(scope.row)"
              :disabled="!scope.row.state"
              >删除</el-button
            >
          </template>
        </el-table-column>
        <!-- 操作行 -->
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
    </el-card>
    <!-- 卡片主体区域 -->
    <!-- 文章预览组件 -->
    <articles-preview
      :isArticlePageShow.sync="isArticlesPreviewshow"
      :thisRow="thisRow"
    ></articles-preview>
    <!-- 文章预览组件 -->
    <!-- 文章增添组件 -->
    <articles-add
      :isArticleAddShow.sync="isArticlesAddshow"
      :articls="articls"
      :edit="edit"
    ></articles-add>
    <!-- 文章增添组件 -->
    <div class="playbig" v-show="vedioShow">
      <video :src="videoURL" class="videoplay" :controls="controls"></video>

      <div class="close" @click="close">关</div>
    </div>
  </div>
</template>

<script>
import { list, remove, detail } from '@/api/hmmm/articles' // api接口
import articlesPreview from '../components/articles-preview' // 文章详情组件
import articlesAdd from '../components/articles-add' // 文章增添组件

export default {
  data() {
    return {
      // 查询参数
      query: {
        page: 1,
        pagesize: 10,
        keyword: '',
        state: null
      },
      // 下拉选框
      options: [
        {
          value: 1,
          label: '已启用'
        },
        {
          value: 0,
          label: '已禁用'
        }
      ],
      // 下拉菜单数据
      value: '',
      // 表单数据
      form: {
        name: ''
      },
      // 表格数据
      tableData: [],
      // 按钮禁用
      isDisabled: true,
      // 列表总数
      total: 0,
      // 文章详情显示
      isArticlesPreviewshow: false,
      // 文章增添显示
      isArticlesAddshow: false,
      thisRow: {}, // 当前行信息(组件传值)
      articls: {}, // 获取的文章信息
      edit: false, // 判断是不是编辑状态
      videoURL: '',
      vedioShow: false,
      controls: false
    }
  },

  components: {
    articlesPreview, // 文章详情组件
    articlesAdd // 文章增添组件
  },

  created() {
    this.loadList()
  },

  watch: {
    isArticlesAddshow() {
      this.loadList()
    }
  },

  methods: {
    // 数据初始化
    async loadList() {
      try {
        const { data } = await list(this.query)
        console.log(data, 321123)
        this.total = data.counts
        this.tableData = data.items
      } catch (error) {
        this.$message({
          message: '亲！网页走丢了奥',
          type: 'warning'
        })
      }
    },

    // 清除表单内容
    clearInput() {
      this.form.name = ''
      this.value = null
    },

    // 删除行
    onRemoveRandoms(row) {
      this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      })
        .then(() => {
          try {
            remove(row)
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
          this.loadList()
        })
        .catch(() => {
          this.$message({
            type: 'info',
            message: '已取消删除'
          })
        })
    },

    // 每页数据变化事件
    handleSizeChange(el) {
      this.query.pagesize = el
      this.loadList()
    },

    // 当前页
    handleCurrentChange(el) {
      this.query.page = el
      this.loadList()
    },

    // 输入框数据获取
    onInput() {
      this.query.keyword = this.form.name
    },

    // 搜索事件
    onSearch() {
      this.loadList()
    },

    // 按钮禁用事件
    onDisabled(el) {
      console.log(el)
      el.state = !el.state
    },

    // 下拉框选择事件
    selectChange(el) {
      this.query.state = el
    },

    // 文章预览会话框显示事件
    onPreview(row) {
      this.isArticlesPreviewshow = true
      this.thisRow = row
    },

    // 增添文章会话框事件
    onIsArticlesAddshow() {
      this.isArticlesAddshow = true
      this.edit = false
    },

    // 文章编辑会话框显示事件
    async onEditArticle(row) {
      const { data } = await detail(row)
      this.articls = data
      this.isArticlesAddshow = true
      this.edit = true
    },

    onVideoShow(el) {
      this.vedioShow = true
      this.videoURL = el
      this.controls = true
    },

    close() {
      this.vedioShow = false
      this.controls = false
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
  /deep/.cell {
    .el-button {
      border: none;
      span {
        display: inline-block;
      }
    }
  }
  // 电影小图标
  .filmSpan {
    color: blue;
    margin-left: 5px;
    cursor: pointer;
    font-size: 20px;
    vertical-align: middle;
  }
  .playbig {
    position: fixed;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    background-color: #6666;
    cursor: pointer;
    z-index: 1999999;
  }
  .videoplay {
    width: 800px;
  }
  .close {
    position: absolute;
    top: -100px;
    left: 50%;
    width: 50px;
    height: 50px;
    transform: translateX(-50%);
    background-color: rgba(0, 0, 0, 0.5);
    border-radius: 50%;
    color: #fff;

    text-align: center;
    line-height: 50px;
  }
}
</style>
