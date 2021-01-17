<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="用户ID" prop="userId">
      <el-input v-model="dataForm.userId" placeholder="用户ID"></el-input>
    </el-form-item>
    <el-form-item label="健康码" prop="healthImg">
      <el-input v-model="dataForm.healthImg" placeholder="健康码"></el-input>
    </el-form-item>
    <el-form-item label="经纬度" prop="position">
      <el-input v-model="dataForm.position" placeholder="经纬度"></el-input>
    </el-form-item>
    <el-form-item label="体温" prop="temperature">
      <el-input v-model="dataForm.temperature" placeholder="体温"></el-input>
    </el-form-item>
    <el-form-item label="是否正常 0：正常 1：不正常" prop="isnormal">
      <el-input v-model="dataForm.isnormal" placeholder="是否正常 0：正常 1：不正常"></el-input>
    </el-form-item>
    <el-form-item label="是否与风险人员接触 0：否 1：是" prop="isCatDangerous">
      <el-input v-model="dataForm.isCatDangerous" placeholder="是否与风险人员接触 0：否 1：是"></el-input>
    </el-form-item>
    <el-form-item label="活动区域" prop="zoneAction">
      <el-input v-model="dataForm.zoneAction" placeholder="活动区域"></el-input>
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
          healthImg: '',
          position: '',
          temperature: '',
          isnormal: '',
          isCatDangerous: '',
          zoneAction: '',
          remark: ''
        },
        dataRule: {
          userId: [
            { required: true, message: '用户ID不能为空', trigger: 'blur' }
          ],
          healthImg: [
            { required: true, message: '健康码不能为空', trigger: 'blur' }
          ],
          position: [
            { required: true, message: '经纬度不能为空', trigger: 'blur' }
          ],
          temperature: [
            { required: true, message: '体温不能为空', trigger: 'blur' }
          ],
          isnormal: [
            { required: true, message: '是否正常 0：正常 1：不正常不能为空', trigger: 'blur' }
          ],
          isCatDangerous: [
            { required: true, message: '是否与风险人员接触 0：否 1：是不能为空', trigger: 'blur' }
          ],
          zoneAction: [
            { required: true, message: '活动区域不能为空', trigger: 'blur' }
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
              url: this.$http.adornUrl(`/epi/healthinfo/info/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.userId = data.healthInfo.userId
                this.dataForm.healthImg = data.healthInfo.healthImg
                this.dataForm.position = data.healthInfo.position
                this.dataForm.temperature = data.healthInfo.temperature
                this.dataForm.isnormal = data.healthInfo.isnormal
                this.dataForm.isCatDangerous = data.healthInfo.isCatDangerous
                this.dataForm.zoneAction = data.healthInfo.zoneAction
                this.dataForm.remark = data.healthInfo.remark
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
              url: this.$http.adornUrl(`/epi/healthinfo/${!this.dataForm.id ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.dataForm.id || undefined,
                'userId': this.dataForm.userId,
                'healthImg': this.dataForm.healthImg,
                'position': this.dataForm.position,
                'temperature': this.dataForm.temperature,
                'isnormal': this.dataForm.isnormal,
                'isCatDangerous': this.dataForm.isCatDangerous,
                'zoneAction': this.dataForm.zoneAction,
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
