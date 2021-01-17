<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="外键epi_task表id" prop="taskId">
      <el-input v-model="dataForm.taskId" placeholder="外键epi_task表id"></el-input>
    </el-form-item>
    <el-form-item label="外键 user表id" prop="userId">
      <el-input v-model="dataForm.userId" placeholder="外键 user表id"></el-input>
    </el-form-item>
    <el-form-item label="状态 0：待完成 1：已完成" prop="status">
      <el-input v-model="dataForm.status" placeholder="状态 0：待完成 1：已完成"></el-input>
    </el-form-item>
    <el-form-item label="接收时间" prop="receiveTime">
      <el-input v-model="dataForm.receiveTime" placeholder="接收时间"></el-input>
    </el-form-item>
    <el-form-item label="备注" prop="remark">
      <el-input v-model="dataForm.remark" placeholder="备注"></el-input>
    </el-form-item>
    </el-form>
    <span slot="footer" class="dialog-footer">
      <el-button @click="visible = false">取消</el-button>
      <el-button type="primary" @click="dataFormSubmit()">确定</el-button>
    </span>
  </el-dialog>
</template>

<script>
  export default {
    data () {
      return {
        visible: false,
        dataForm: {
          id: 0,
          taskId: '',
          userId: '',
          status: '',
          receiveTime: '',
          remark: ''
        },
        dataRule: {
          taskId: [
            { required: true, message: '外键epi_task表id不能为空', trigger: 'blur' }
          ],
          userId: [
            { required: true, message: '外键 user表id不能为空', trigger: 'blur' }
          ],
          status: [
            { required: true, message: '状态 0：待完成 1：已完成不能为空', trigger: 'blur' }
          ],
          receiveTime: [
            { required: true, message: '接收时间不能为空', trigger: 'blur' }
          ],
          remark: [
            { required: true, message: '备注不能为空', trigger: 'blur' }
          ]
        }
      }
    },
    methods: {
      init (id) {
        this.dataForm.id = id || 0
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.id) {
            this.$http({
              url: this.$http.adornUrl(`/epi/tasksand/info/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.taskId = data.taskSand.taskId
                this.dataForm.userId = data.taskSand.userId
                this.dataForm.status = data.taskSand.status
                this.dataForm.receiveTime = data.taskSand.receiveTime
                this.dataForm.remark = data.taskSand.remark
              }
            })
          }
        })
      },
      // 表单提交
      dataFormSubmit () {
        this.$refs['dataForm'].validate((valid) => {
          if (valid) {
            this.$http({
              url: this.$http.adornUrl(`/epi/tasksand/${!this.dataForm.id ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.dataForm.id || undefined,
                'taskId': this.dataForm.taskId,
                'userId': this.dataForm.userId,
                'status': this.dataForm.status,
                'receiveTime': this.dataForm.receiveTime,
                'remark': this.dataForm.remark
              })
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.$message({
                  message: '操作成功',
                  type: 'success',
                  duration: 1500,
                  onClose: () => {
                    this.visible = false
                    this.$emit('refreshDataList')
                  }
                })
              } else {
                this.$message.error(data.msg)
              }
            })
          }
        })
      }
    }
  }
</script>
