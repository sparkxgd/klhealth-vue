<template>
  <div class="mod-config">
    <el-form :inline="true" :model="dataForm" @keyup.enter.native="getDataList()">
      <el-form-item>
        <el-input v-model="dataForm.key" placeholder="参数名" clearable></el-input>
      </el-form-item>
      <el-form-item>
        <el-button @click="getDataList()">查询</el-button>
        <el-button v-if="isAuth('epi:task:save')" type="primary" @click="addOrUpdateHandle()">新增</el-button>
        <el-button v-if="isAuth('epi:task:delete')" type="danger" @click="deleteHandle()" :disabled="dataListSelections.length <= 0">批量删除</el-button>
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
        prop="taskType"
        header-align="center"
        align="center"
        label="任务类型"
        width="80">
        <template slot-scope="scope">
          <el-tag v-if="scope.row.taskType === 0" size="small" type="danger">作业</el-tag>
          <el-tag v-else-if="scope.row.taskType === 1" size="small" type="danger">预留</el-tag>
        </template>
      </el-table-column>

      <el-table-column
        prop="title"
        header-align="center"
        align="center"
        label="任务标题"
        width="180">
      </el-table-column>

      <el-table-column
        prop="sandTarget"
        header-align="center"
        align="center"
        label="发布目标"
        width="80">
        <template slot-scope="scope">
          <el-tag v-if="scope.row.sandTarget === 0" size="small" type="warning">所有用户</el-tag>
          <el-tag v-else-if="scope.row.sandTarget === 1" size="small" type="success">学生</el-tag>
          <el-tag v-else-if="scope.row.sandTarget === 2" size="small">教师</el-tag>
          <el-tag v-else size="small" type="warning">其他</el-tag>
        </template>
      </el-table-column>
      <el-table-column
        prop="startTime"
        header-align="center"
        align="center"
        label="开始时间"
        width="180">
      </el-table-column>

      <el-table-column
        prop="endTime"
        header-align="center"
        align="center"
        label="结束时间"
        width="180">
      </el-table-column>

      <el-table-column
        prop="sandType"
        header-align="center"
        align="center"
        label="类型"
        width="80">
        <template slot-scope="scope">
          <el-tag v-if="scope.row.sandType === 1" size="small" type="danger">紧急</el-tag>
          <el-tag v-else size="small">一般</el-tag>
        </template>
      </el-table-column>

      <el-table-column
        prop="status"
        header-align="center"
        align="center"
        label="状态"
        width="80">
        <template slot-scope="scope">
          <el-tag v-if="scope.row.status === 0" size="small" type="info">未执行</el-tag>
          <el-tag v-else-if="scope.row.status === 1" size="small" type="warning">执行中</el-tag>
          <el-tag v-else-if="scope.row.status === 2" size="small" type="success">完成</el-tag>
          <el-tag v-else-if="scope.row.status === 3" size="small" type="warning">未完成</el-tag>
          <el-tag v-else size="small" type="danger">异常</el-tag>
        </template>
      </el-table-column>
      <el-table-column
        prop="username"
        header-align="center"
        align="center"
        label="创建任务人"
        width="100">
      </el-table-column>

      <el-table-column
        prop="desc"
        header-align="center"
        align="center"
        label="任务描述">
      </el-table-column>
      <el-table-column
        prop="remark"
        header-align="center"
        align="center"
        label="备注"
        width="100">
      </el-table-column>
      <el-table-column
        header-align="center"
        align="center"
        label="操作">
        <template slot-scope="scope">
          <el-button v-if="scope.row.status === 0" type="text" size="small" @click="startTask(scope.row.id)">开始执行</el-button>

          <el-button type="text" size="small" @click="addOrUpdateHandle(scope.row.id)">修改</el-button>
          <el-button type="text" size="small" @click="deleteHandle(scope.row.id)">删除</el-button>
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
  </div>
</template>

<script>
  import AddOrUpdate from './task-add-or-update'
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
        addOrUpdateVisible: false
      }
    },
    components: {
      AddOrUpdate
    },
    activated () {
      this.getDataList()
    },
    methods: {
      // 获取数据列表
      getDataList () {
        this.dataListLoading = true
        this.$http({
          url: this.$http.adornUrl('/epi/task/list'),
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
      // 执行任务
      startTask (id) {
        this.$confirm(`确定对任务[id=${id}]开始执行操作?`, '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          this.$http({
            url: this.$http.adornUrl('/epi/task/startTask'),
            method: 'post',
            data: this.$http.adornData(id, false)
          }).then(({data}) => {
            if (data && data.code === 0) {
              this.$message({
                message: data.msg,
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
            url: this.$http.adornUrl('/epi/task/delete'),
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
