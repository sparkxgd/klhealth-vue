<template>
  <el-dialog :title="!dataForm.id ? '新增' : '修改'" :close-on-click-modal="false" :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">

      <el-form-item label="标题" prop="title">
        <el-input v-model="dataForm.title" placeholder="标题"></el-input>
      </el-form-item>

      <el-form-item label="内容" prop="content">
        <el-input v-model="dataForm.content" placeholder="内容"></el-input>
      </el-form-item>

      <el-form-item label="发消息人" prop="userId">
        <el-input v-model="dataForm.userId" placeholder="发消息人ID"></el-input>
      </el-form-item>

      <el-form-item label="发送时间" prop="sandTime">

        <el-date-picker v-model="dataForm.sandTime" type="date" format="yyyy-MM-dd" value-format="yyyy-MM-dd"
          placeholder="选择日期时间">
        </el-date-picker>
      </el-form-item>

      <!-- 消息类型 0：一般 1：紧急 -->
      <el-form-item label="消息类型" prop="sandType">
        <el-select v-model="dataForm.sandType" placeholder="请选择消息类型">
          <el-option label="一般" value="一般"></el-option>
          <el-option label="紧急" value="紧急"></el-option>
        </el-select>
      </el-form-item>

      <!-- 信息状态 0：待发送 1：已发送 -1：异常-->
      <el-form-item label="信息状态" prop="status">
        <el-select v-model="dataForm.status" placeholder="请选择消息类型">
          <el-option label="待发送" value="待发送"></el-option>
          <el-option label="已发送" value="已发送"></el-option>
          <el-option label="异常" value="异常"></el-option>
        </el-select>
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
    data() {
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
          title: [{
            required: true,
            message: '标题不能为空',
            trigger: 'blur'
          }],
          content: [{
            required: true,
            message: '内容不能为空',
            trigger: 'blur'
          }],
          userId: [{
            required: true,
            message: '发消息人ID不能为空',
            trigger: 'blur'
          }],
          sandTime: [{
            required: true,
            message: '发送时间不能为空',
            trigger: 'blur'
          }],
          sandType: [{
            required: true,
            message: '消息类型 0：一般 1：紧急不能为空',
            trigger: 'blur'
          }],
          status: [{
            required: true,
            message: '信息状态 0：待发送 1：已发送 -1：异常不能为空',
            trigger: 'blur'
          }]
        }
      }
    },
    methods: {
      init(data) {
        this.visible = true
        if (data) {
          this.dataForm.id = data.id
          this.dataForm.title = data.title
          this.dataForm.content = data.content
          this.dataForm.userId = data.userId
          this.dataForm.sandTime = data.sandTime
          this.dataForm.sandType = data.sandType == 0 ? "一般" : "紧急"
          if (data.status == 0) this.dataForm.status = "待发送"
          else if (data.status == 1) this.dataForm.status = "已发送"
          else this.dataForm.status = "异常"
          this.dataForm.remark = data.remark
        }else{
          this.dataForm.id = ''
          this.dataForm.title = ''
          this.dataForm.content = ''
          this.dataForm.userId = ''
          this.dataForm.sandTime =new Date()
          this.dataForm.sandType = '一般'
          this.dataForm.status = '待发送'
          this.dataForm.remark = ''
        }
      },
      // 表单提交
      dataFormSubmit() {
        this.$refs['dataForm'].validate((valid) => {
          if (valid) {
            var data = {};
            data.id = this.dataForm.id
            data.title = this.dataForm.title
            data.content = this.dataForm.content
            data.sandTime = this.dataForm.sandTime
            data.userId = this.dataForm.userId
            if (this.dataForm.sandType == "一般") {
              data.sandType = 0
            } else {
              data.sandType = 1
            }
            if (this.dataForm.status == "待发送") {
              data.status = 0
            } else if (this.dataForm.status == "已发送") {
              data.status = 1
            } else {
              data.status = -1
            }
            data.remark = this.dataForm.remark

            this.$http({
              url: this.$http.adornUrl(`/epi/message/${!this.dataForm.id ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData(data)
            }).then(({
              data
            }) => {
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
