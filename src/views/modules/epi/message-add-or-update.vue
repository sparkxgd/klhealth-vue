<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="标题" prop="title">
      <el-input v-model="dataForm.title" placeholder="标题"></el-input>
    </el-form-item>
    <el-form-item label="内容" prop="content">
      <el-input v-model="dataForm.content" placeholder="内容"></el-input>
    </el-form-item>
    <el-form-item label="发消息人ID" prop="userId">
      <el-input v-model="dataForm.userId" placeholder="发消息人ID"></el-input>
    </el-form-item>
    <el-form-item label="发送时间" prop="sandTime">
      <el-input v-model="dataForm.sandTime" placeholder="发送时间"></el-input>
    </el-form-item>
    <el-form-item label="消息类型 0：一般 1：紧急" prop="sandType">
      <el-input v-model="dataForm.sandType" placeholder="消息类型 0：一般 1：紧急"></el-input>
    </el-form-item>
    <el-form-item label="信息状态 0：待发送 1：已发送 -1：异常" prop="status">
      <el-input v-model="dataForm.status" placeholder="信息状态 0：待发送 1：已发送 -1：异常"></el-input>
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
          title: '',
          content: '',
          userId: '',
          sandTime: '',
          sandType: '',
          status: '',
          remark: ''
        },
        dataRule: {
          title: [
            { required: true, message: '标题不能为空', trigger: 'blur' }
          ],
          content: [
            { required: true, message: '内容不能为空', trigger: 'blur' }
          ],
          userId: [
            { required: true, message: '发消息人ID不能为空', trigger: 'blur' }
          ],
          sandTime: [
            { required: true, message: '发送时间不能为空', trigger: 'blur' }
          ],
          sandType: [
            { required: true, message: '消息类型 0：一般 1：紧急不能为空', trigger: 'blur' }
          ],
          status: [
            { required: true, message: '信息状态 0：待发送 1：已发送 -1：异常不能为空', trigger: 'blur' }
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
              url: this.$http.adornUrl(`/epi/message/info/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.title = data.message.title
                this.dataForm.content = data.message.content
                this.dataForm.userId = data.message.userId
                this.dataForm.sandTime = data.message.sandTime
                this.dataForm.sandType = data.message.sandType
                this.dataForm.status = data.message.status
                this.dataForm.remark = data.message.remark
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
              url: this.$http.adornUrl(`/epi/message/${!this.dataForm.id ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.dataForm.id || undefined,
                'title': this.dataForm.title,
                'content': this.dataForm.content,
                'userId': this.dataForm.userId,
                'sandTime': this.dataForm.sandTime,
                'sandType': this.dataForm.sandType,
                'status': this.dataForm.status,
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
