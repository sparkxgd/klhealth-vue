<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="学工号，教师工号 其他" prop="no">
      <el-input v-model="dataForm.no" placeholder="学工号，教师工号 其他"></el-input>
    </el-form-item>
    <el-form-item label="用户ID" prop="userId">
      <el-input v-model="dataForm.userId" placeholder="用户ID"></el-input>
    </el-form-item>
    <el-form-item label="真实姓名" prop="name">
      <el-input v-model="dataForm.name" placeholder="真实姓名"></el-input>
    </el-form-item>
    <el-form-item label="身份证号" prop="idCard">
      <el-input v-model="dataForm.idCard" placeholder="身份证号"></el-input>
    </el-form-item>
    <el-form-item label="类型，0：学生，1：教师，2：其他" prop="type">
      <el-input v-model="dataForm.type" placeholder="类型，0：学生，1：教师，2：其他"></el-input>
    </el-form-item>
    <el-form-item label="性别 0：女 男：1" prop="sex">
      <el-input v-model="dataForm.sex" placeholder="性别 0：女 男：1"></el-input>
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
          no: '',
          userId: '',
          name: '',
          idCard: '',
          type: '',
          sex: '',
          remark: ''
        },
        dataRule: {
          no: [
            { required: true, message: '学工号，教师工号 其他不能为空', trigger: 'blur' }
          ],
          userId: [
            { required: true, message: '用户ID不能为空', trigger: 'blur' }
          ],
          name: [
            { required: true, message: '真实姓名不能为空', trigger: 'blur' }
          ],
          idCard: [
            { required: true, message: '身份证号不能为空', trigger: 'blur' }
          ],
          type: [
            { required: true, message: '类型，0：学生，1：教师，2：其他不能为空', trigger: 'blur' }
          ],
          sex: [
            { required: true, message: '性别 0：女 男：1不能为空', trigger: 'blur' }
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
              url: this.$http.adornUrl(`/epi/userbaseinfo/info/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.no = data.userBaseInfo.no
                this.dataForm.userId = data.userBaseInfo.userId
                this.dataForm.name = data.userBaseInfo.name
                this.dataForm.idCard = data.userBaseInfo.idCard
                this.dataForm.type = data.userBaseInfo.type
                this.dataForm.sex = data.userBaseInfo.sex
                this.dataForm.remark = data.userBaseInfo.remark
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
              url: this.$http.adornUrl(`/epi/userbaseinfo/${!this.dataForm.id ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.dataForm.id || undefined,
                'no': this.dataForm.no,
                'userId': this.dataForm.userId,
                'name': this.dataForm.name,
                'idCard': this.dataForm.idCard,
                'type': this.dataForm.type,
                'sex': this.dataForm.sex,
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
