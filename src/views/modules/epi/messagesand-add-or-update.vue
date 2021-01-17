<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="外键 epi_message表id" prop="msgId">
      <el-input v-model="dataForm.msgId" placeholder="外键 epi_message表id"></el-input>
    </el-form-item>
    <el-form-item label="发送给谁 user表id" prop="userId">
      <el-input v-model="dataForm.userId" placeholder="发送给谁 user表id"></el-input>
    </el-form-item>
    <el-form-item label="消息状态 0：待接收 1：已接收" prop="status">
      <el-input v-model="dataForm.status" placeholder="消息状态 0：待接收 1：已接收"></el-input>
    </el-form-item>
    <el-form-item label="接收时间" prop="sandTime">
      <el-input v-model="dataForm.sandTime" placeholder="接收时间"></el-input>
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
          msgId: '',
          userId: '',
          status: '',
          sandTime: '',
          remark: ''
        },
        dataRule: {
          msgId: [
            { required: true, message: '外键 epi_message表id不能为空', trigger: 'blur' }
          ],
          userId: [
            { required: true, message: '发送给谁 user表id不能为空', trigger: 'blur' }
          ],
          status: [
            { required: true, message: '消息状态 0：待接收 1：已接收不能为空', trigger: 'blur' }
          ],
          sandTime: [
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
              url: this.$http.adornUrl(`/epi/messagesand/info/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.msgId = data.messageSand.msgId
                this.dataForm.userId = data.messageSand.userId
                this.dataForm.status = data.messageSand.status
                this.dataForm.sandTime = data.messageSand.sandTime
                this.dataForm.remark = data.messageSand.remark
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
              url: this.$http.adornUrl(`/epi/messagesand/${!this.dataForm.id ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.dataForm.id || undefined,
                'msgId': this.dataForm.msgId,
                'userId': this.dataForm.userId,
                'status': this.dataForm.status,
                'sandTime': this.dataForm.sandTime,
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
