<template>
  <div>
    <!-- 面包屑 -->
    <el-breadcrumb separator-class="el-icon-arrow-right">
      <el-breadcrumb-item :to="{ path: '/' }">首页</el-breadcrumb-item>
      <el-breadcrumb-item>门店</el-breadcrumb-item>
      <el-breadcrumb-item>提现</el-breadcrumb-item>
    </el-breadcrumb>
    <!-- 卡片视图显示 -->
    <el-card>
      <el-row :gutter="20">
        <!-- 搜索框 -->
        <el-col :span="8" class="grid">
          <el-input v-model="book.shopName" placeholder="请输入名称">
            <el-button
              slot="append"
              icon="el-icon-search"
              @click="getData"
            ></el-button>
          </el-input>
        </el-col>
        <el-col :span="4"> </el-col>
      </el-row>
    </el-card>
    <!-- 显示的数据 -->
    <el-table
      :data="
        tableData.slice((currentPage - 1) * pageSize, currentPage * pageSize)
      "
      border
      stripe
    >
      <el-table-column prop="shopName" label="门店名称"> </el-table-column>
      <el-table-column prop="sale" label="金额"> </el-table-column>
      <el-table-column prop="wtName" label="提现类型"> </el-table-column>
      <el-table-column prop="uname" label="收款人姓名"> </el-table-column>
      <el-table-column prop="baccount" label="提现账号"> </el-table-column>
      <el-table-column prop="wdate" label="提现时间"> </el-table-column>
      <el-table-column label="审核状态">
        <template slot-scope="scope">
          <p v-if="tableData[scope.$index].wTid == 0" style="color: red">
            提现中
          </p>
          <p v-if="tableData[scope.$index].wTid == 1" style="color: blue">
            已提现
          </p>
        </template>
      </el-table-column>
    </el-table>
    <div class="block" style="margin-top: 15px">
      <el-pagination
        @size-change="handleSizeChange"
        @current-change="handleCurrentChange"
        :current-page="currentPage"
        :page-sizes="[1, 2, 5, 50]"
        :page-size="pageSize"
        layout="total, sizes, prev, pager, next, jumper"
        :total="tableData.length"
      >
      </el-pagination>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      tableData: [],
      book:{
        shopName:''
      },
      currentPage: 1, //当前页码
      pageSize: 2, //每页的数据条数
      total: 0, //默认总条数为一条
    };
  },
  methods: {
    getData() {
      this.axios({
        url: "http://localhost:15850/api/GetWithdraw2",
        method: "get",
        params: {
            shopName:this.book.shopName,
          },
      }).then((res) => {
        console.log(res);
        this.tableData = res.data.data;
      });
    },
    // 初始页currentPage、初始每页数据数pagesize和数据data
    handleSizeChange: function (size) {
      this.pageSize = size;
      console.log(this.pageSize); //每页下拉显示数据
    },
    handleCurrentChange: function (currentPage) {
      this.currentPage = currentPage;
      console.log(this.currentPage); //点击第几页
    },
  },
  mounted() {
    this.getData();
  },
  //监听pagesize 改变的事件
  handleSizeChange(newSize) {
    console.log(newSize);
  },
  //监听 页码值 改变的事件
  handleCurrentChange(newPage) {
    console.log(newPage);
  },
};
</script>

<style>
/* 上方控件的样式 */
.el-breadcrumb {
  margin-bottom: 15px;
  font-size: 15px;
}
.el-card {
  box-shadow: 0 1px 1px rgba(0, 0, 0, 0.15) !important;
}
.el-table td,
.el-table th {
  margin-top: 15px;
  text-align: center !important;
}
</style>