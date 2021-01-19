<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
      <el-form-item label="任务类型" prop="taskType">
        <el-input v-model="dataForm.taskType" placeholder="任务类型，预留，0：作业"></el-input>
      </el-form-item>
      <el-form-item label="任务标题" prop="title">
        <el-input v-model="dataForm.title" placeholder="任务标题"></el-input>
      </el-form-item>
      <el-form-item label="任务描述" prop="desc">
        <el-input v-model="dataForm.desc" placeholder="任务描述"></el-input>
      </el-form-item>
      <el-form-item label="创建任务人id" prop="userId">
        <el-input v-model="dataForm.userId" placeholder="外键user表id"></el-input>
      </el-form-item>
      <el-form-item label="开始时间" prop="startTime">
        <el-input v-model="dataForm.startTime" placeholder="开始时间"></el-input>
      </el-form-item>
      <el-form-item label="结束时间" prop="endTime">
        <el-input v-model="dataForm.endTime" placeholder="结束时间"></el-input>
      </el-form-item>
      <el-form-item label="类型" prop="sandType">
        <el-input v-model="dataForm.sandType" placeholder="类型 0：一般 1：紧急"></el-input>
      </el-form-item>
      <el-form-item label="状态" prop="status">
        <el-input v-model="dataForm.status" placeholder="状态 0：未执行 1：执行中 2：完成 3：未完成 -1：异常"></el-input>
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
          taskType: '',
          title: '',
          desc: '',
          userId: '',
          startTime: '',
          endTime: '',
          sandType: '',
          status: '',
          remark: ''
        },
        dataRule: {
          taskType: [
            { required: true, message: '任务类型不能为空', trigger: 'blur' }
          ],
          title: [
            { required: true, message: '任务标题不能为空', trigger: 'blur' }
          ],
          desc: [
            { required: true, message: '任务描述不能为空', trigger: 'blur' }
          ],
          userId: [
            { required: true, message: '创建任务人不能为空', trigger: 'blur' }
          ],
          startTime: [
            { required: true, message: '开始时间不能为空', trigger: 'blur' }
          ],
          endTime: [
            { required: true, message: '结束时间不能为空', trigger: 'blur' }
          ],
          sandType: [
            { required: true, message: '类型不能为空', trigger: 'blur' }
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
              url: this.$http.adornUrl(`/epi/task/info/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.taskType = data.task.taskType
                this.dataForm.title = data.task.title
                this.dataForm.desc = data.task.desc
                this.dataForm.userId = data.task.userId
                this.dataForm.startTime = data.task.startTime
                this.dataForm.endTime = data.task.endTime
                this.dataForm.sandType = data.task.sandType
                this.dataForm.status = data.task.status
                this.dataForm.remark = data.task.remark
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
              url: this.$http.adornUrl(`/epi/task/${!this.dataForm.id ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.dataForm.id || undefined,
                'taskType': this.dataForm.taskType,
                'title': this.dataForm.title,
                'desc': this.dataForm.desc,
                'userId': this.dataForm.userId,
                'startTime': this.dataForm.startTime,
                'endTime': this.dataForm.endTime,
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
