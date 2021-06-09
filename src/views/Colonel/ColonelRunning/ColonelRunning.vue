<template>
  <!-- 团长审核 -->
  <div>
    <!-- 面包屑 -->
    <el-breadcrumb separator-class="el-icon-arrow-right">
      <el-breadcrumb-item :to="{ path: '/' }">首页</el-breadcrumb-item>
      <el-breadcrumb-item>团长</el-breadcrumb-item>
      <el-breadcrumb-item>佣金流水</el-breadcrumb-item>
    </el-breadcrumb>
    <!-- 卡片视图显示 -->
    <el-card>
      <el-row :gutter="20">
        <!-- 搜索框 -->
        <span class="demonstration">时间范围：</span>
        <el-date-picker
          v-model="book.time"
          type="datetimerange"
          unlink-panels
          range-separator="至"
          start-placeholder="开始日期"
          end-placeholder="结束日期"
          value-format="yyyy-MM-dd"
          style="margin-left: 12px; margin-right: 20px"
        >
        </el-date-picker>
        <span>佣金状态：</span>
        <el-select
          v-model="book.rstate"
          filterable
          clearable
          placeholder="请选择"
          style="width: 150px; margin-left: 12px; margin-right: 20px"
        >
          <el-option
            v-for="item in options"
            :key="item.tid"
            :label="item.name"
            :value="item.tid"
          >
          </el-option>
        </el-select>
        <span>订单状态：</span>
        <el-select
          v-model="book.rorderState"
          filterable
          clearable
          placeholder="请选择"
          style="width: 150px; margin-left: 12px; margin-right: 20px"
        >
          <el-option
            v-for="item in option"
            :key="item.tid"
            :label="item.name"
            :value="item.tid"
          >
          </el-option>
        </el-select>
        <el-button type="primary" @click="getData">搜索</el-button>
        <el-button type="primary" @click="exportExcelSelect"
          >导出Excel</el-button
        >
      </el-row>
    </el-card>
    <div>
      <!-- 显示的数据 -->
      <el-table
        :data="
          tableData.slice((currentPage - 1) * pageSize, currentPage * pageSize)
        "
        border
        stripe
        @selection-change="handleSelectionChange"
      >
        >
        <el-table-column prop="rid" label="ID"> </el-table-column>
        <el-table-column prop="mpic" label="头像">
          <!-- 图片操作 -->
          <template slot-scope="scope">
            <img :src="scope.row.mpic" style="width: 100px; height: 50px" />
          </template>
        </el-table-column>
        <el-table-column prop="rimg" label="拥金获得者/姓名"> </el-table-column>
        <el-table-column prop="rcommissionType" label="佣金类型">
        </el-table-column>
        <el-table-column prop="rcommissioncount" label="所得佣金">
        </el-table-column>
        <el-table-column label="状态">
          <template slot-scope="scope">
            <p v-if="tableData[scope.$index].rstate == 0">待结算</p>
            <p v-if="tableData[scope.$index].rstate == 1">已结算</p>
            <p v-if="tableData[scope.$index].rstate == 2">已拒绝</p>
          </template>
        </el-table-column>
        <el-table-column prop="rendTime" label="结算时间"> </el-table-column>
        <el-table-column label="当前订单状态">
          <template slot-scope="scope">
            <p v-if="tableData[scope.$index].rorderState == 0">待发货</p>
            <p v-if="tableData[scope.$index].rorderState == 1">待团长收货</p>
            <p v-if="tableData[scope.$index].rorderState == 2">待用户收货</p>
            <p v-if="tableData[scope.$index].rorderState == 3">已退款</p>
            <p v-if="tableData[scope.$index].rorderState == 4">已完成</p>
          </template>
        </el-table-column>
      </el-table>
      <!-- 导出表格 -->
      <el-dialog title="表格保存预览" width="70%" :visible.sync="selectWindow">
        <el-table :data="selectData" id="selectTable" height="380px">
          <el-table-column prop="rid" label="ID"> </el-table-column>
          <el-table-column prop="mpic" label="头像">
            <!-- 图片操作 -->
            <template slot-scope="scope">
              <img :src="scope.row.mpic" style="width: 100px; height: 50px" />
            </template>
          </el-table-column>
          <el-table-column prop="rimg" label="拥金获得者/姓名">
          </el-table-column>
          <el-table-column prop="rcommissionType" label="佣金类型">
          </el-table-column>
          <el-table-column prop="rcommissioncount" label="所得佣金">
          </el-table-column>
          <el-table-column label="状态">
            <template slot-scope="scope">
              <p v-if="tableData[scope.$index].rstate == 0">待结算</p>
              <p v-if="tableData[scope.$index].rstate == 1">已结算</p>
              <p v-if="tableData[scope.$index].rstate == 2">已拒绝</p>
            </template>
          </el-table-column>
          <el-table-column prop="rendTime" label="结算时间"> </el-table-column>
          <el-table-column label="当前订单状态">
            <template slot-scope="scope">
              <p v-if="tableData[scope.$index].rorderState == 0">待发货</p>
              <p v-if="tableData[scope.$index].rorderState == 1">待团长收货</p>
              <p v-if="tableData[scope.$index].rorderState == 2">待用户收货</p>
              <p v-if="tableData[scope.$index].rorderState == 3">已退款</p>
              <p v-if="tableData[scope.$index].rorderState == 4">已完成</p>
            </template>
          </el-table-column>
        </el-table>
        <div slot="footer" class="dialog-footer">
          <el-button type="primary" @click="exportExcel">确定保存</el-button>
        </div>
      </el-dialog>
    </div>

    <!-- 分页 -->
    <div class="block">
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
import htmlToExcel from "@/utils/htmlToExcel";
export default {
  name: "ExcelPage",
  data() {
    return {
      tableData: [],
      //导出操作
      selectWindow: false,
      selectData: [],
      //查询字段集合
      book: {
        time: "",
        rstate: [],
        rorderState: [],
      },
      //初始化佣金状态下拉(tid、name为随意起的名称)
      options: [
        { tid: -1, name: "全部" },
        { tid: 0, name: "待结算" },
        { tid: 1, name: "已结算" },
        { tid: 2, name: "已拒绝" },
      ],
      //初始化订单状态下拉(tid、name为随意起的名称)
      option: [
        { tid: -1, name: "全部" },
        { tid: 0, name: "待发货" },
        { tid: 1, name: "待团长发货" },
        { tid: 2, name: "待用户发货" },
        { tid: 3, name: "已退款" },
        { tid: 4, name: "已完成" },
      ],
      currentPage: 1, //当前页码
      pageSize: 2, //每页的数据条数
      total: 0, //默认总条数为一条
    };
  },
  methods: {
    //显示
    getData() {
      var n = "";
      if (this.book.time != "") {
        n = this.book.time;
      }
      this.axios({
        url: "http://localhost:15850/api/GetRunning",
        method: "get",
        params: {
          rstate: this.book.rstate,
          rorderState: this.book.rorderState,
          time: `${n}`,
        },
      }).then((res) => {
        console.log(res);
        this.tableData = res.data.data;
        this.selectData = res.data.data;
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
    //导出操作
    exportExcel() {
      htmlToExcel.getExcel("#selectTable", "导出的自定义标题");
    },
    exportExcelSelect() {
       this.selectWindow = true;
      if (this.selectData.length < 1) {
        this.$message.error("请选择要导出的内容！");
        return false;
      }
     
    },
    handleSelectionChange(val) {
      this.selectData = val;
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