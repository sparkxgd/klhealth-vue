<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="标题" prop="title">
      <el-input v-model="dataForm.title" placeholder="标题"></el-input>
    </el-form-item>
    <el-form-item label="副标题" prop="subhead">
      <el-input v-model="dataForm.subhead" placeholder="副标题"></el-input>
    </el-form-item>
    <el-form-item label="内容" prop="content">
      <el-input v-model="dataForm.content" placeholder="内容"></el-input>
    </el-form-item>
    <el-form-item label="新闻类型，外键。类型表" prop="newsType">
      <el-input v-model="dataForm.newsType" placeholder="新闻类型，外键。类型表"></el-input>
    </el-form-item>
    <el-form-item label="类型 1：原创 2：转载 3：其他" prop="type">
      <el-input v-model="dataForm.type" placeholder="类型 1：原创 2：转载 3：其他"></el-input>
    </el-form-item>
    <el-form-item label="作者名字，原创就是创建者" prop="author">
      <el-input v-model="dataForm.author" placeholder="作者名字，原创就是创建者"></el-input>
    </el-form-item>
    <el-form-item label="审核人,外键 user表id" prop="reviewer">
      <el-input v-model="dataForm.reviewer" placeholder="审核人,外键 user表id"></el-input>
    </el-form-item>
    <el-form-item label="审核时间" prop="rviewTime">
      <el-input v-model="dataForm.rviewTime" placeholder="审核时间"></el-input>
    </el-form-item>
    <el-form-item label="发布时间" prop="releaseTime">
      <el-input v-model="dataForm.releaseTime" placeholder="发布时间"></el-input>
    </el-form-item>
    <el-form-item label="文章状态 0：创建 1审核 2：发布 -1：异常" prop="status">
      <el-input v-model="dataForm.status" placeholder="文章状态 0：创建 1审核 2：发布 -1：异常"></el-input>
    </el-form-item>
    <el-form-item label="创建者 外键 user表id" prop="creater">
      <el-input v-model="dataForm.creater" placeholder="创建者 外键 user表id"></el-input>
    </el-form-item>
    <el-form-item label="创建时间" prop="ceateTime">
      <el-input v-model="dataForm.ceateTime" placeholder="创建时间"></el-input>
    </el-form-item>
    <el-form-item label="浏览量" prop="lookNum">
      <el-input v-model="dataForm.lookNum" placeholder="浏览量"></el-input>
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
          subhead: '',
          content: '',
          newsType: '',
          type: '',
          author: '',
          reviewer: '',
          rviewTime: '',
          releaseTime: '',
          status: '',
          creater: '',
          ceateTime: '',
          lookNum: '',
          remark: ''
        },
        dataRule: {
          title: [
            { required: true, message: '标题不能为空', trigger: 'blur' }
          ],
          subhead: [
            { required: true, message: '副标题不能为空', trigger: 'blur' }
          ],
          content: [
            { required: true, message: '内容不能为空', trigger: 'blur' }
          ],
          newsType: [
            { required: true, message: '新闻类型，外键。类型表不能为空', trigger: 'blur' }
          ],
          type: [
            { required: true, message: '类型 1：原创 2：转载 3：其他不能为空', trigger: 'blur' }
          ],
          author: [
            { required: true, message: '作者名字，原创就是创建者不能为空', trigger: 'blur' }
          ],
          reviewer: [
            { required: true, message: '审核人,外键 user表id不能为空', trigger: 'blur' }
          ],
          rviewTime: [
            { required: true, message: '审核时间不能为空', trigger: 'blur' }
          ],
          releaseTime: [
            { required: true, message: '发布时间不能为空', trigger: 'blur' }
          ],
          status: [
            { required: true, message: '文章状态 0：创建 1审核 2：发布 -1：异常不能为空', trigger: 'blur' }
          ],
          creater: [
            { required: true, message: '创建者 外键 user表id不能为空', trigger: 'blur' }
          ],
          ceateTime: [
            { required: true, message: '创建时间不能为空', trigger: 'blur' }
          ],
          lookNum: [
            { required: true, message: '浏览量不能为空', trigger: 'blur' }
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
              url: this.$http.adornUrl(`/epi/news/info/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.title = data.news.title
                this.dataForm.subhead = data.news.subhead
                this.dataForm.content = data.news.content
                this.dataForm.newsType = data.news.newsType
                this.dataForm.type = data.news.type
                this.dataForm.author = data.news.author
                this.dataForm.reviewer = data.news.reviewer
                this.dataForm.rviewTime = data.news.rviewTime
                this.dataForm.releaseTime = data.news.releaseTime
                this.dataForm.status = data.news.status
                this.dataForm.creater = data.news.creater
                this.dataForm.ceateTime = data.news.ceateTime
                this.dataForm.lookNum = data.news.lookNum
                this.dataForm.remark = data.news.remark
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
              url: this.$http.adornUrl(`/epi/news/${!this.dataForm.id ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.dataForm.id || undefined,
                'title': this.dataForm.title,
                'subhead': this.dataForm.subhead,
                'content': this.dataForm.content,
                'newsType': this.dataForm.newsType,
                'type': this.dataForm.type,
                'author': this.dataForm.author,
                'reviewer': this.dataForm.reviewer,
                'rviewTime': this.dataForm.rviewTime,
                'releaseTime': this.dataForm.releaseTime,
                'status': this.dataForm.status,
                'creater': this.dataForm.creater,
                'ceateTime': this.dataForm.ceateTime,
                'lookNum': this.dataForm.lookNum,
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
