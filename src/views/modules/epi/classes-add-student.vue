<template>
  <el-dialog title="添加学生" :close-on-click-modal="false" :visible.sync="visible">

    <el-row :gutter="20">
      <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">

        <el-form-item label="学号" prop="stu_no">
          <el-input v-model="dataForm.stu_no" placeholder="学号"></el-input>
        </el-form-item>

        <el-form-item label="班级" prop="class_name">
          <el-input readonly="readonly" v-model="dataForm.class_name" placeholder="班级,外键，班级表id"></el-input>
        </el-form-item>

        <el-form-item label="状态" prop="status">
          <el-select v-model="dataForm.status" placeholder="请选择状态">
            <el-option label="正常" value="0"></el-option>
            <el-option label="异常" value="-1"></el-option>
            <el-option label="退出" value="1"></el-option>
            <el-option label="休学" value="2"></el-option>
          </el-select>
        </el-form-item>

        <el-form-item label="用户名" prop="username">
          <el-input v-model="dataForm.username" placeholder="用户名"></el-input>
        </el-form-item>

        <el-form-item label="真实姓名" prop="ral_name">
          <el-input v-model="dataForm.ral_name" placeholder="真实姓名"></el-input>
        </el-form-item>

        <el-button type="primary" @click="onSubmit">添加</el-button>
        <el-button @click="visible = false">取消</el-button>
      </el-form>
    </el-row>
  </el-dialog>
</template>

<script>
  export default {
    data() {
      return {
        visible: false,
        dataForm: {
          stu_no: '',
          class_id: '',
          status: '',
          username: '',
          ral_name: '',
          class_name: ''
        },
        dataRule: {
          stu_no: [{
            required: true,
            message: '学号不能为空',
            trigger: 'blur'
          }],
          class_id: [{
            required: true,
            message: '班级ID不能为空',
            trigger: 'blur'
          }],
          status: [{
            required: true,
            message: '请选择状态',
            trigger: 'blur'
          }],
          username: [{
            required: true,
            message: '用户名不能为空',
            trigger: 'blur'
          }],
          ral_name: [{
            required: true,
            message: '真实姓名不能为空',
            trigger: 'blur'
          }]
        }
      }
    },
    methods: {
      init(data) {
        this.visible = true
        if(data){
          this.dataForm.class_id = data.id
          this.dataForm.class_name=data.name
        }
      },
      /* 提交数据*/
      onSubmit() {
        this.$refs['dataForm'].validate((valid) => {
          if (valid) {
            var data={};
            data.no=this.dataForm.stu_no;
            data.clsId=this.dataForm.class_id;
            data.status=this.dataForm.status;
            data.username=this.dataForm.username;
            data.name=this.dataForm.ral_name;
            data.clsName=this.dataForm.class_name;
            this.$http({
              url: this.$http.adornUrl('/epi/studentclasses/save'),
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
