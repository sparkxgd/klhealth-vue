<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="被管理班级	外键，班级表id" prop="claId">
      <el-input v-model="dataForm.claId" placeholder="被管理班级	外键，班级表id"></el-input>
    </el-form-item>
    <el-form-item label="管理人（班主任，辅导员）	外键，user表id" prop="userId">
      <el-input v-model="dataForm.userId" placeholder="管理人（班主任，辅导员）	外键，user表id"></el-input>
    </el-form-item>
    <el-form-item label="创建时间" prop="createTime">
      <el-input v-model="dataForm.createTime" placeholder="创建时间"></el-input>
    </el-form-item>
    <el-form-item label="状态	0：正常，-1：异常" prop="status">
      <el-input v-model="dataForm.status" placeholder="状态	0：正常，-1：异常"></el-input>
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
          claId: '',
          userId: '',
          createTime: '',
          status: ''
        },
        dataRule: {
          claId: [
            { required: true, message: '被管理班级	外键，班级表id不能为空', trigger: 'blur' }
          ],
          userId: [
            { required: true, message: '管理人（班主任，辅导员）	外键，user表id不能为空', trigger: 'blur' }
          ],
          createTime: [
            { required: true, message: '创建时间不能为空', trigger: 'blur' }
          ],
          status: [
            { required: true, message: '状态	0：正常，-1：异常不能为空', trigger: 'blur' }
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
              url: this.$http.adornUrl(`/epi/classmanage/info/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.claId = data.classManage.claId
                this.dataForm.userId = data.classManage.userId
                this.dataForm.createTime = data.classManage.createTime
                this.dataForm.status = data.classManage.status
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
              url: this.$http.adornUrl(`/epi/classmanage/${!this.dataForm.id ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.dataForm.id || undefined,
                'claId': this.dataForm.claId,
                'userId': this.dataForm.userId,
                'createTime': this.dataForm.createTime,
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
