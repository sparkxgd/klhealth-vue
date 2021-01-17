<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="第三方登录ID 微信ID qqID" prop="accountId">
      <el-input v-model="dataForm.accountId" placeholder="第三方登录ID 微信ID qqID"></el-input>
    </el-form-item>
    <el-form-item label="用户ID" prop="userId">
      <el-input v-model="dataForm.userId" placeholder="用户ID"></el-input>
    </el-form-item>
    <el-form-item label="第三方的昵称" prop="nickname">
      <el-input v-model="dataForm.nickname" placeholder="第三方的昵称"></el-input>
    </el-form-item>
    <el-form-item label="头像" prop="headImg">
      <el-input v-model="dataForm.headImg" placeholder="头像"></el-input>
    </el-form-item>
    <el-form-item label="类型 0：微信 1:QQ 2：其他" prop="type">
      <el-input v-model="dataForm.type" placeholder="类型 0：微信 1:QQ 2：其他"></el-input>
    </el-form-item>
    <el-form-item label="绑定时间" prop="updateTime">
      <el-input v-model="dataForm.updateTime" placeholder="绑定时间"></el-input>
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
          accountId: '',
          userId: '',
          nickname: '',
          headImg: '',
          type: '',
          updateTime: '',
          remark: ''
        },
        dataRule: {
          accountId: [
            { required: true, message: '第三方登录ID 微信ID qqID不能为空', trigger: 'blur' }
          ],
          userId: [
            { required: true, message: '用户ID不能为空', trigger: 'blur' }
          ],
          nickname: [
            { required: true, message: '第三方的昵称不能为空', trigger: 'blur' }
          ],
          headImg: [
            { required: true, message: '头像不能为空', trigger: 'blur' }
          ],
          type: [
            { required: true, message: '类型 0：微信 1:QQ 2：其他不能为空', trigger: 'blur' }
          ],
          updateTime: [
            { required: true, message: '绑定时间不能为空', trigger: 'blur' }
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
              url: this.$http.adornUrl(`/epi/userbanding/info/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.accountId = data.userBanding.accountId
                this.dataForm.userId = data.userBanding.userId
                this.dataForm.nickname = data.userBanding.nickname
                this.dataForm.headImg = data.userBanding.headImg
                this.dataForm.type = data.userBanding.type
                this.dataForm.updateTime = data.userBanding.updateTime
                this.dataForm.remark = data.userBanding.remark
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
              url: this.$http.adornUrl(`/epi/userbanding/${!this.dataForm.id ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.dataForm.id || undefined,
                'accountId': this.dataForm.accountId,
                'userId': this.dataForm.userId,
                'nickname': this.dataForm.nickname,
                'headImg': this.dataForm.headImg,
                'type': this.dataForm.type,
                'updateTime': this.dataForm.updateTime,
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
