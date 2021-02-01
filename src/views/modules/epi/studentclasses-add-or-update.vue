<template>
  <el-dialog :title="!dataForm.id ? '新增' : '修改'" :close-on-click-modal="false" :visible.sync="visible">

    <el-form  :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">

      <el-form-item label="学号" prop="stu_no">
        <el-input v-model="dataForm.stu_no" placeholder="学号"></el-input>
      </el-form-item>

      <el-form-item label="班级" prop="class_name">
        <el-select v-model="dataForm.class_name" placeholder="请选择班级">
          <el-option v-for="item in this.options" :key="item.id" :label="item.label" :value="item.name">
          </el-option>
        </el-select>
      </el-form-item>

      <el-form-item label="状态" prop="status">
        <el-select v-model="dataForm.statusT" placeholder="请选择状态">
          <el-option label="正常" value="正常"></el-option>
          <el-option label="异常" value="异常"></el-option>
          <el-option label="退出" value="退出"></el-option>
          <el-option label="休学" value="休学"></el-option>
        </el-select>
      </el-form-item>

      <el-form-item label="用户名" prop="username">
        <el-input v-model="dataForm.username" placeholder="用户名"></el-input>
      </el-form-item>

      <el-form-item label="真实姓名" prop="ral_name">
        <el-input v-model="dataForm.ral_name" placeholder="真实姓名"></el-input>
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
        loading:true,
        options: [],
        InfoData:{},
        dataForm: {
          id: 0,
          stu_no: '',
          class_id: 0,
          username: '',
          ral_name: '',
          class_name: '',
          statusT:'正常',
          status:0
        },
        dataRule: {
          stu_no: [{
            required: true,
            message: '学号不能为空',
            trigger: 'blur'
          }],
          class_id: [{
            required: true,
            message: '班级,外键，班级表id不能为空',
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
        this.InfoData=data
        this.getClassInfo()
      },
      /* 渲染数据*/
      SetDataInfo(data){
        if (data) {
          this.dataForm.id = data.id
          this.dataForm.stu_no = data.no
          this.dataForm.username = data.username
          this.dataForm.status=data.status
          this.dataForm.class_id=data.clsId
          this.dataForm.ral_name = data.name
          this.dataForm.class_name=data.clsName
          if(data.status==0){
            this.dataForm.statusT="正常"
          }else if(data.status==-1){
            this.dataForm.statusT="异常"
          }else if(data.status==1){
            this.dataForm.statusT="退出"
          }else{
            this.dataForm.statusT="休学"
          }
        } else {
          this.dataForm.id = 0
          this.dataForm.stu_no = ''
          this.dataForm.class_id = ''
          this.dataForm.username = ''
          this.dataForm.class_id = ''
          this.dataForm.ral_name = ''
          this.dataForm.class_name=''
          this.dataForm.statusT='正常'
        }
      },
      /* 获取班级表*/
      getClassInfo() {
        this.$http({
          url: this.$http.adornUrl('/epi/classes/list'),
          method: 'get',
          params: this.$http.adornParams({
            'page': 1,
            'limit': 5,
            'key': ''
          })
        }).then(({
          data
        }) => {
          if (data && data.code === 0) {
            this.options = data.page.list
            /* 获取CLASS_NAME*/
            this.options.forEach((item,index)=>{
              if(item.id==this.dataForm.class_id){
                this.dataForm.class_name=item.name
              }
            })
            this.SetDataInfo(this.InfoData)
          }
          this.loading = false
        })
      },
      // 表单提交
      dataFormSubmit() {
        /* 提交*/
        this.$refs['dataForm'].validate((valid) => {
          if (valid) {
            var data={};
            data.id=this.dataForm.id
            data.no=this.dataForm.stu_no
            this.options.forEach((item,index)=>{
              if(item.name==this.dataForm.class_name){
                data.clsId=item.id
              }
            })
            if(this.dataForm.statusT=='正常'){
              data.status=0
            }else if(this.dataForm.statusT=='异常'){
              data.status=-1
            }else if(this.dataForm.statusT=='退出'){
              data.status=1
            }else{
              data.status=2
            }
            data.username=this.dataForm.username
            data.name=this.dataForm.ral_name
            data.clsName=this.dataForm.class_name
            this.$http({
              url: this.$http.adornUrl(`/epi/studentclasses/${!this.dataForm.id ? 'save' : 'update'}`),
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
