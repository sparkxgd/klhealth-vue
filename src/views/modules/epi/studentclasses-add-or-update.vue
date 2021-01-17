<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="学号" prop="no">
      <el-input v-model="dataForm.no" placeholder="学号"></el-input>
    </el-form-item>
    <el-form-item label="班级,外键，班级表id" prop="clsId">
      <el-input v-model="dataForm.clsId" placeholder="班级,外键，班级表id"></el-input>
    </el-form-item>
    <el-form-item label="加入时间" prop="addTime">
      <el-input v-model="dataForm.addTime" placeholder="加入时间"></el-input>
    </el-form-item>
    <el-form-item label="退出时间" prop="exitTime">
      <el-input v-model="dataForm.exitTime" placeholder="退出时间"></el-input>
    </el-form-item>
    <el-form-item label="更新时间" prop="updateTime">
      <el-input v-model="dataForm.updateTime" placeholder="更新时间"></el-input>
    </el-form-item>
    <el-form-item label="状态 0：正常，-1：异常,1:退出，2：休学" prop="status">
      <el-input v-model="dataForm.status" placeholder="状态 0：正常，-1：异常,1:退出，2：休学"></el-input>
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
          no: '',
          clsId: '',
          addTime: '',
          exitTime: '',
          updateTime: '',
          status: ''
        },
        dataRule: {
          no: [
            { required: true, message: '学号不能为空', trigger: 'blur' }
          ],
          clsId: [
            { required: true, message: '班级,外键，班级表id不能为空', trigger: 'blur' }
          ],
          addTime: [
            { required: true, message: '加入时间不能为空', trigger: 'blur' }
          ],
          exitTime: [
            { required: true, message: '退出时间不能为空', trigger: 'blur' }
          ],
          updateTime: [
            { required: true, message: '更新时间不能为空', trigger: 'blur' }
          ],
          status: [
            { required: true, message: '状态 0：正常，-1：异常,1:退出，2：休学不能为空', trigger: 'blur' }
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
              url: this.$http.adornUrl(`/epi/studentclasses/info/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.no = data.studentClasses.no
                this.dataForm.clsId = data.studentClasses.clsId
                this.dataForm.addTime = data.studentClasses.addTime
                this.dataForm.exitTime = data.studentClasses.exitTime
                this.dataForm.updateTime = data.studentClasses.updateTime
                this.dataForm.status = data.studentClasses.status
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
              url: this.$http.adornUrl(`/epi/studentclasses/${!this.dataForm.id ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.dataForm.id || undefined,
                'no': this.dataForm.no,
                'clsId': this.dataForm.clsId,
                'addTime': this.dataForm.addTime,
                'exitTime': this.dataForm.exitTime,
                'updateTime': this.dataForm.updateTime,
                'status': this.dataForm.status
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
