<template>
  <div class="mod-config">
    <el-form :inline="true" :model="dataForm" @keyup.enter.native="getDataList()">
      <el-form-item>
        <el-input v-model="dataForm.key" placeholder="班级名称" clearable></el-input>
      </el-form-item>
      <el-form-item>
        <el-button @click="getDataList()">查询</el-button>
        <el-button v-if="isAuth('epi:classes:save')" type="primary" @click="addOrUpdateHandle()">新增</el-button>
        <el-button v-if="isAuth('epi:classes:delete')" type="danger" @click="deleteHandle()" :disabled="dataListSelections.length <= 0">批量删除</el-button>
      </el-form-item>
    </el-form>
    <el-table
      :data="dataList"
      border
      v-loading="dataListLoading"
      @selection-change="selectionChangeHandle"
      style="width: 100%;">
      <el-table-column
        type="selection"
        header-align="center"
        align="center"
        width="50">
      </el-table-column>

      <el-table-column
        prop="name"
        header-align="center"
        align="center"
        label="班级名称"
        width="100">
      </el-table-column>

      <el-table-column
        prop="majorName"
        header-align="center"
        align="center"
        label="所属专业"
        width="100">
      </el-table-column>

      <el-table-column
        prop="no"
        header-align="center"
        align="center"
        label="班级编号"
        width="100">
      </el-table-column>

      <el-table-column
        prop="grade"
        header-align="center"
        align="center"
        label="年级"
        width="100">
      </el-table-column>

      <el-table-column
        prop="graduateTime"
        header-align="center"
        align="center"
        label="毕业时间"
        width="100">
      </el-table-column>

      <el-table-column
        prop="updateTime"
        header-align="center"
        align="center"
        label="更新时间"
        width="180">
      </el-table-column>

      <el-table-column
        prop="desc"
        header-align="center"
        align="center"
        label="描述"
        width="100">
      </el-table-column>

      <el-table-column
        prop="status"
        header-align="center"
        align="center"
        label="状态"
        width="100">
        <template slot-scope="scope">
          <el-tag v-if="scope.row.status === -1" size="small" type="danger">异常</el-tag>
          <el-tag v-else size="small">正常</el-tag>
        </template>
      </el-table-column>

      <el-table-column
        header-align="center"
        align="center"
        label="操作">
        <template slot-scope="scope">
          <el-button type="primary" size="mini" @click="addStudentHandle(scope.row)">添加学生</el-button>
          <el-button type="success" size="mini" @click="addOrUpdateHandle(scope.row.id)">修改</el-button>
          <el-button type="danger" size="mini" @click="deleteHandle(scope.row.id)">删除</el-button>
        </template>
      </el-table-column>
    </el-table>
    <el-pagination
      @size-change="sizeChangeHandle"
      @current-change="currentChangeHandle"
      :current-page="pageIndex"
      :page-sizes="[10, 20, 50, 100]"
      :page-size="pageSize"
      :total="totalPage"
      layout="total, sizes, prev, pager, next, jumper">
    </el-pagination>
    <!-- 弹窗, 新增 / 修改 -->
    <add-or-update v-if="addOrUpdateVisible" ref="addOrUpdate" @refreshDataList="getDataList"></add-or-update>
    <add-student v-if="addStudentVisible" ref="addStudent" @refreshDataList="getDataList"></add-student>
  </div>
</template>

<script>
  import AddOrUpdate from './classes-add-or-update'
  import AddStudent from './classes-add-student'
  export default {
    data () {
      return {
        dataForm: {
          key: ''
        },
        dataList: [],
        pageIndex: 1,
        pageSize: 10,
        totalPage: 0,
        dataListLoading: false,
        dataListSelections: [],
        addOrUpdateVisible: false,
        addStudentVisible: false
      }
    },
    components: {
      AddOrUpdate,
      AddStudent
    },
    activated () {
      this.getDataList()
    },
    methods: {
      // 获取数据列表
      getDataList () {
        this.dataListLoading = true
        this.$http({
          url: this.$http.adornUrl('/epi/classes/list'),
          method: 'get',
          params: this.$http.adornParams({
            'page': this.pageIndex,
            'limit': this.pageSize,
            'key': this.dataForm.key
          })
        }).then(({data}) => {
          if (data && data.code === 0) {
            this.dataList = data.page.list
            this.totalPage = data.page.totalCount
          } else {
            this.dataList = []
            this.totalPage = 0
          }
          this.dataListLoading = false
        })
      },
      // 每页数
      sizeChangeHandle (val) {
        this.pageSize = val
        this.pageIndex = 1
        this.getDataList()
      },
      // 当前页
      currentChangeHandle (val) {
        this.pageIndex = val
        this.getDataList()
      },
      // 多选
      selectionChangeHandle (val) {
        this.dataListSelections = val
      },
      // 新增 / 修改
      addOrUpdateHandle (id) {
        this.addOrUpdateVisible = true
        this.$nextTick(() => {
          this.$refs.addOrUpdate.init(id)
        })
      },
      // 添加学生
      addStudentHandle (id) {
        this.addStudentVisible = true
        this.$nextTick(() => {
          this.$refs.addStudent.init(id)
        })
      },
      // 删除
      deleteHandle (id) {
        var ids = id ? [id] : this.dataListSelections.map(item => {
          return item.id
        })
        this.$confirm(`确定对[id=${ids.join(',')}]进行[${id ? '删除' : '批量删除'}]操作?`, '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          this.$http({
            url: this.$http.adornUrl('/epi/classes/delete'),
            method: 'post',
            data: this.$http.adornData(ids, false)
          }).then(({data}) => {
            if (data && data.code === 0) {
              this.$message({
                message: '操作成功',
                type: 'success',
                duration: 1500,
                onClose: () => {
                  this.getDataList()
                }
              })
            } else {
              this.$message.error(data.msg)
            }
          })
        })
      }
    }
  }
</script>
