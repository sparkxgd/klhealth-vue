<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="外键 user表id" prop="userId">
      <el-input v-model="dataForm.userId" placeholder="外键 user表id"></el-input>
    </el-form-item>
    <el-form-item label="运动步数" prop="steps">
      <el-input v-model="dataForm.steps" placeholder="运动步数"></el-input>
    </el-form-item>
    <el-form-item label="运动开始时间" prop="startTime">
      <el-input v-model="dataForm.startTime" placeholder="运动开始时间"></el-input>
    </el-form-item>
    <el-form-item label="运动结束时间" prop="endTime">
      <el-input v-model="dataForm.endTime" placeholder="运动结束时间"></el-input>
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
          userId: '',
          steps: '',
          startTime: '',
          endTime: '',
          remark: ''
        },
        dataRule: {
          userId: [
            { required: true, message: '外键 user表id不能为空', trigger: 'blur' }
          ],
          steps: [
            { required: true, message: '运动步数不能为空', trigger: 'blur' }
          ],
          startTime: [
            { required: true, message: '运动开始时间不能为空', trigger: 'blur' }
          ],
          endTime: [
            { required: true, message: '运动结束时间不能为空', trigger: 'blur' }
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
              url: this.$http.adornUrl(`/epi/motioninfo/info/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.userId = data.motionInfo.userId
                this.dataForm.steps = data.motionInfo.steps
                this.dataForm.startTime = data.motionInfo.startTime
                this.dataForm.endTime = data.motionInfo.endTime
                this.dataForm.remark = data.motionInfo.remark
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
              url: this.$http.adornUrl(`/epi/motioninfo/${!this.dataForm.id ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.dataForm.id || undefined,
                'userId': this.dataForm.userId,
                'steps': this.dataForm.steps,
                'startTime': this.dataForm.startTime,
                'endTime': this.dataForm.endTime,
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
