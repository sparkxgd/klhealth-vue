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

    <el-form-item label="新闻类型" prop="newsType">
      <el-select v-model="dataForm.newsType" placeholder="请选择">
        <el-option v-for="item in options" :key="item.id" :label="item.name" :value="item.id"></el-option>
      </el-select>
    </el-form-item>
    <el-form-item label="类型" prop="type">
      <el-radio-group v-model="dataForm.type">
        <el-radio  :label="1" border>原创</el-radio>
        <el-radio  :label="2" border>转载</el-radio>
        <el-radio  :label="3" border>其他</el-radio>
      </el-radio-group>
    </el-form-item>
    <el-form-item label="作者" prop="author">
      <el-input v-model="dataForm.author" placeholder="作者名字，原创就是创建者"></el-input>
    </el-form-item>
      <el-form-item label="内容" prop="content">
        <div class="mod-demo-ueditor">
          <script :id="ueId" class="ueditor-box" type="text/plain" style="width: 100%; height: 260px;"></script>
        </div>
      </el-form-item>
    </el-form>
    <span slot="footer" class="dialog-footer">
      <el-button @click="visible = false">取消</el-button>
      <el-button type="primary" @click="dataFormSubmit()">确定</el-button>
    </span>
  </el-dialog>
</template>

<script>
  import ueditor from 'ueditor'
  export default {
    data () {
      return {
        ue: null,
        ueId: `J_ueditorBox_${new Date().getTime()}`,
        ueContent: '',
        visible: false,
        dataForm: {
          id: 0,
          title: '',
          subhead: '',
          content: '',
          newsType: 2,
          type: 1,
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
          author: [
            { required: true, message: '作者名字，原创就是创建者不能为空', trigger: 'blur' }
          ]
        },
        options: [
          { name: '疫情新闻', id: 1 }, { name: '学校新闻', id: 2 }]
      }
    },
    methods: {
      init (id) {
        this.dataForm.id = id || 0
        this.visible = true
        this.$nextTick(() => {
          this.ue = ueditor.getEditor(this.ueId, {
            // serverUrl: '', // 服务器统一请求接口路径
          })
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
