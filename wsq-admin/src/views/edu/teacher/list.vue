<template>
  <div class="app-container">

    <el-table v-loading = "listLoading" :data="list" element-loading-text="数据加载中" border fit highlight-current-row>
      <el-table-column label="序号" width="70" align="center">
        <template slot-scope="scope">
          {{ (page - 1)*limit + scope.$index +1 }}
        </template>
      </el-table-column>
      <el-table-column prop="name" label="名称" width="80" />

      <el-table-column label="头衔" width="80">
        <template slot-scope="scope">
          {{ scope.row.level===1?'高级讲师':'首席讲师' }}
        </template>
      </el-table-column>

      <el-table-column prop="intro" label="资历" />

      <el-table-column prop="gmtCreate" label="添加时间" width="160"/>

      <el-table-column prop="sort" label="排序" width="60" />

      <el-table-column label="操作" width="200" align="center">
        <template slot-scope="scope">
          <router-link :to="'/edu/teacher/edit/'+scope.row.id">
            <el-button type="primary" size="mini" icon="el-icon-edit">修改</el-button>
          </router-link>
          <el-button type="danger" size="mini" icon="el-icon-delete" @click="removeDataById(scope.row.id)">删除</el-button>
        </template>
      </el-table-column>

    </el-table>

    <!-- 分页 -->
    <el-pagination
      :current-page="page"
      :page-size="limit"
      :total="total"
      style="padding: 30px 0; text-align: center;"
      layout="total, prev, pager, next, jumper"
      @current-change="fetchData"
    />
  </div>
</template>

<script>
import teacher from '@/api/edu/teacher'
export default {
  data() { // 定义数据
    return {
      listLoading: true, // 是否显示loading
      list: null, // 数据列表
      total: 0,
      page: 1,
      limit: 5,
      searchObj: {} // 查询条件
    }
  },

  created() { // 页面加载时获取数据
    this.fetchData(1)
  },

  methods: {
    fetchData(page) {
      console.log('加载list')
      this.page = page
      this.listLoading = true
      teacher.getPageList(this.page, this.limit, this.searchObj).then(Response => {
        // debugger
        if (Response.success === true) {
          this.list = Response.data.rows
          this.total = Response.data.total
        }
        this.listLoading = false
      })
    },

    removeDataById(userId) {
      this.$confirm('确定删除嘛', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        return teacher.removeById(userId)
      }).then(response => {
        this.fetchData(this.page)
        this.$message({
          type: 'success',
          message: '删除成功!'
        })
      }).catch((response) => { // 失败
        if (response === 'cancel') {
          this.$message({
            type: 'info',
            message: '已取消删除'
          })
        }
      })
    }

  }

}
</script>
