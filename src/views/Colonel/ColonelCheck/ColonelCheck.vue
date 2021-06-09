<template>
  <!-- 团长审核 -->
  <div>
    <!-- 面包屑 -->
    <el-breadcrumb separator-class="el-icon-arrow-right">
      <el-breadcrumb-item :to="{ path: '/' }">首页</el-breadcrumb-item>
      <el-breadcrumb-item>团长</el-breadcrumb-item>
      <el-breadcrumb-item>团长审核</el-breadcrumb-item>
    </el-breadcrumb>
    <!-- 卡片视图显示 -->
    <el-card>
      <el-row :gutter="20">
        <!-- 搜索框 -->
        <span class="demonstration">时间范围</span>
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
        <span>审核状态</span>
        <el-select
          v-model="book.tauditState"
          filterable
          clearable
          placeholder="请选择"
          style="width: 200px; margin-left: 12px; margin-right: 20px"
        >
          <el-option
            v-for="item in options"
            :key="item.tid"
            :label="item.name"
            :value="item.tid"
          >
          </el-option>
        </el-select>
        <span>关键词</span>
        <el-input
          v-model="book.tName"
          placeholder="请输入"
          style="width: 200px; margin-left: 12px; margin-right: 12px"
        ></el-input>
        <el-button type="primary" @click="getData">搜索</el-button>
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
      <el-table-column type="index" label="ID"> </el-table-column>
      <el-table-column prop="tpic" label="头像">
        <!-- 图片操作 -->
        <template slot-scope="scope">
          <img :src="scope.row.tpic" style="width: 100px; height: 50px" />
        </template>
      </el-table-column>
      <el-table-column prop="tName" label="姓名"> </el-table-column>
      <el-table-column prop="tPhone" label="手机号"> </el-table-column>
      <el-table-column prop="tCity" label="所属城市"> </el-table-column>
      <el-table-column prop="tSiteMessage" label="自提点信息">
      </el-table-column>
      <el-table-column prop="tcost" label="团长配送费"> </el-table-column>
      <el-table-column prop="uid" label="审核人姓名"> </el-table-column>
      <el-table-column prop="tapplyTime" label="申请时间"> </el-table-column>
      <el-table-column prop="taudittime" label="审核时间"> </el-table-column>
      <el-table-column label="审核状态">
        <template slot-scope="scope">
          <p
            v-if="tableData[scope.$index].tauditState == 0"
            style="color: green"
          >
            审核通过
          </p>
          <p
            v-if="tableData[scope.$index].tauditState == 1"
            style="color: orange"
          >
            待审核
          </p>
          <p v-if="tableData[scope.$index].tauditState == 2" style="color: red">
            审核未通过
          </p>
        </template>
      </el-table-column>
      <el-table-column prop="" label="操作" width="200px">
        <template slot-scope="scope">
          <el-button
            size="mini"
            type="success"
            @click.native="handleEdit(scope.$index, scope.row, 0)"
            :disabled="
              (scope.row.tauditState != 1) & (scope.row.tauditState != 2)
                ? true
                : false
            "
            >通过</el-button
          >
          <el-button
            size="mini"
            type="danger"
            @click.native="handleDelete(scope.$index, scope.row, 2)"
            :disabled="
              (scope.row.tauditState != 1) & (scope.row.tauditState != 2)
                ? true
                : false
            "
            >不通过</el-button
          >
        </template>
      </el-table-column>
    </el-table>
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
    <!-- 新增对话框 -->
    <el-dialog title="提示" :visible.sync="addDialogVisible" width="50%">
      <!-- 内容主体区域 -->
      <span>这是一段信息</span>
      <!-- 底部区域 -->
      <span slot="footer" class="dialog-footer">
        <el-button @click="addDialogVisible = false">取 消</el-button>
        <el-button type="primary" @click="addDialogVisible = false"
          >确 定</el-button
        >
      </span>
    </el-dialog>
  </div>
</template>

<script>
export default {
  data() {
    return {
      tableData: [],
      //查询字段集合
      book: {
        time: "",
        tName: "",
        tauditState: [],
      },
      //初始化下拉(tid、name为随意起的名称)
      options: [
        { tid: -1, name: "全部" },
        { tid: 0, name: "审核通过" },
        { tid: 1, name: "待审核" },
        { tid: 2, name: "审核未通过" },
      ],
      currentPage: 1, //当前页码
      pageSize: 2, //每页的数据条数
      total: 0, //默认总条数为一条
      //控制对话框的显示与隐藏
      addDialogVisible: false,
    };
  },
  methods: {
    getData() {
      var n = "";
      if (this.book.time != "") {
        n = this.book.time;
      }
      this.axios({
        url: "http://localhost:15850/api/GetShen",
        method: "get",
        params: {
          tauditState: this.book.tauditState,
          tName: this.book.tName,
          time: `${n}`,
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
    //操作按钮
    handleEdit(index, row, st) {
      this.axios({
        url:
          "http://localhost:15850/api/UptState?tid=" +
          row.tid +
          "&tauditState=" +
          st,
        method: "put",
      }).then((d) => {
        if (d.data > 0) {
          this.$message({
            message: "恭喜你，审核成功",
            type: "success",
          });
        } else {
          this.$message.error("错了哦，审核失败");
        }
        this.getData();
      });
    },
    handleDelete(index, row, st) {
      this.axios({
        url:
          "http://localhost:15850/api/UptState?tid=" +
          row.tid +
          "&tauditState=" +
          st,
        method: "put",
      }).then((d) => {
        if (d.data > 0) {
          this.$message({
            message: "恭喜你，修改成功",
            type: "success",
          });
        } else {
          this.$message.error("错了哦，修改失败");
        }
        this.getData();
      });
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