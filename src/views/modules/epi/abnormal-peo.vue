<template>
<div class="epi-abnormal">
    <div class="row-title">异常人员信息</div>
    <el-table v-loading="loading" :data="tableData"  style="width: 100%">
      <el-table-column prop="username" label="用户名" width="150">
      </el-table-column>
      <el-table-column prop="phone" label="手机号" width="150">
      </el-table-column>
      <el-table-column prop="no" label="工号/学号" width="150">
      </el-table-column>
      <el-table-column prop="name" label="姓名" width="150">
      </el-table-column>
      <el-table-column prop="id_card" label="身份证" width="150">
      </el-table-column>
      <el-table-column prop="type" label="类型" width="150">
        <template slot-scope="scope">
          <el-tag v-if="scope.row.type==0">学生</el-tag>
          <el-tag v-else-if="scope.row.type==1">老师</el-tag>
          <el-tag v-else>其他</el-tag>
        </template>
      </el-table-column>
      <el-table-column prop="sex" label="性别" width="150">
        <template slot-scope="scope">
          <el-tag v-if="scope.row.sex==0">女</el-tag>
          <el-tag v-else>男</el-tag>
        </template>
      </el-table-column>
      <el-table-column prop="remark" label="备注" width="150">
      </el-table-column>
    </el-table>
    <!-- page-size设置每页显示条数 total总条数-->
    <el-pagination
      background
      layout="prev, pager, next"
      :page-size="20"
      :total="1000">
    </el-pagination>
  </div>
</template>

<script>
  export default {
      data() {
        return {
          tableData: [{
            username: 'DFM',
            phone: '18386083531',
            no: '2020202018',
            name: '刘财',
            id_card: '500223********2512',
            type: 0,
            sex: 1,
            remark: ''
          }],
          loading: false
        };
      }
    };
</script>

<style>
  .row-title {
    color: #000000;
    font-size: 20px;
    margin: 0 0 20px 0;
  }
</style>
