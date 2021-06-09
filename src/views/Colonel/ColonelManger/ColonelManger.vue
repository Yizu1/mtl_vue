<template>
  <!-- 团长管理 -->
  <div>
    <!-- 面包屑 -->
    <el-breadcrumb separator-class="el-icon-arrow-right">
      <el-breadcrumb-item :to="{ path: '/' }">首页</el-breadcrumb-item>
      <el-breadcrumb-item>团长</el-breadcrumb-item>
      <el-breadcrumb-item>团长管理</el-breadcrumb-item>
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
      <el-table-column prop="" label="操作" width="250px">
        <template slot-scope="scope">
          <el-button
            size="mini"
            :disabled="scope.row.tauditState != 0 ? true : false"
            @click="handleEdit(scope.row.tid)"
            >编辑</el-button
          >
          <el-button
            size="mini"
            :disabled="scope.row.tauditState != 0 ? true : false"
            style="margin-left: 32px"
            >绑定商品</el-button
          >
          <br />
          <el-button
            size="mini"
            :disabled="scope.row.tauditState != 0 ? true : false"
            >添加核销员</el-button
          >
          <el-button
            size="mini"
            :disabled="scope.row.tauditState != 0 ? true : false"
            >查看核销员</el-button
          >
        </template>
      </el-table-column>
    </el-table>
    <!-- 修改对话框 -->
    <el-dialog
      title="编辑"
      :visible.sync="UptDialogVisible"
      width="50%"
      :before-close="UptDialogClosed"
    >
      <!-- 修改内容主体区域 -->
      <el-form
        :model="UptForm"
        ref="UptFormRef"
        label-width="110px"
      >
        <el-form-item label="头像" prop="tpic">
          <!-- 图片操作 -->
          <template>
            <img :src="UptForm.tpic" style="width: 100px; height: 50px" />
          </template>
        </el-form-item>
        <el-form-item label="昵称" prop="tnikeName">
          <el-input v-model="UptForm.tnikeName"></el-input>
        </el-form-item>
        <el-form-item label="性别" prop="tsex">
          <template>
            <p v-if="UptForm.tauditState == 0">男</p>
            <p v-if="UptForm.tauditState == 1">女</p>
          </template>
        </el-form-item>
        <el-form-item label="手机号码" prop="tPhone">
          <el-input
            v-model="UptForm.tPhone"
            placeholder="请输入手机号码"
          ></el-input>
        </el-form-item>
        <el-form-item label="真实姓名" prop="trueName">
          <el-input
            v-model="UptForm.trueName"
            placeholder="真实姓名"
          ></el-input>
        </el-form-item>
        <el-form-item label="团员数量" prop="tcount">
          <p>未设置</p>
        </el-form-item>
        <el-form-item label="上级团长" prop="tParentId">
          <p>无（未设置）</p>
        </el-form-item>
        <el-form-item label="自提点地区" prop="tsitearea">
          <el-input
            v-model="UptForm.tsitearea"
            placeholder="自提点地区"
          ></el-input>
        </el-form-item>
        <el-form-item label="自提点小区" prop="tsiteSmall">
          <el-input
            v-model="UptForm.tsiteSmall"
            placeholder="自提点小区"
          ></el-input>
        </el-form-item>
        <el-form-item label="自提点地址" prop="tsiteAddress">
          <el-input
            v-model="UptForm.tsiteAddress"
            placeholder="自提点地址"
          ></el-input>
        </el-form-item>
        <el-form-item label="自提点坐标">
          <el-input
            v-model="UptForm.tlongitude"
            placeholder="经度"
            style="width: 200px"
          ></el-input>
          <el-input
            v-model="UptForm.tlatitude"
            placeholder="纬度"
            style="width: 200px"
          ></el-input>
        </el-form-item>
        <el-form-item label="地图定位" prop="Tmap">
          <el-input v-model="UptForm.Tmap" placeholder="地图定位"></el-input>
        </el-form-item>
        <el-form-item label="注册时间" prop="tregisterTime">
          <el-input
            v-model="UptForm.tregisterTime"
            placeholder="请输入性别"
          ></el-input>
        </el-form-item>
        <el-form-item label="积分" prop="tintegral">
          <p>未设置</p>
        </el-form-item>
        <el-form-item label="销量额" prop="tsale">
          <p>未设置</p>
        </el-form-item>
        <el-form-item label="送货上门" prop="tIsGoHome">
           <el-switch
            v-model="UptForm.tIsGoHome"
            :active-value="1"
            :inactive-value="0"
          >
          </el-switch>
        </el-form-item>
        <el-form-item label="配送费" prop="tcost">
          <el-input v-model="UptForm.tcost" placeholder="配送费"></el-input>
        </el-form-item>
        <el-form-item label="支付宝" prop="talipay">
          <el-input v-model="UptForm.talipay" placeholder="支付宝"></el-input>
        </el-form-item>
        <el-form-item label="银行卡所在行" prop="tbankWhere">
          <el-input
            v-model="UptForm.tbankWhere"
            placeholder="银行卡所在行"
          ></el-input>
        </el-form-item>
        <el-form-item label="持卡人姓名" prop="tbankCardPerson">
          <el-input
            v-model="UptForm.tbankCardPerson"
            placeholder="持卡人姓名"
          ></el-input>
        </el-form-item>
        <el-form-item label="卡号" prop="tbankCard">
          <el-input v-model="UptForm.tbankCard" placeholder="卡号"></el-input>
        </el-form-item>
      </el-form>
      <!-- 底部区域 -->
      <span
        slot="footer"
        class="dialog-footer"
        style="display: block; text-align: center"
      >
        <el-button type="primary" @click="UptUser">提 交</el-button>
      </span>
    </el-dialog>
    <!-- 分页 -->
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
      //控制修改对话框的显示和隐藏
      UptDialogVisible: false,
      //查询到修改的用户信息对象
      UptForm: {},
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
        url: "http://localhost:15850/api/GetTeam",
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
    //监听修改对话框的关闭事件
    UptDialogClosed() {
      this.$refs.UptFormRef.resetFields(); //控制重置
      this.UptDialogVisible = false; //点击X时关闭
    },
    //修改弹窗显示(反填)
    handleEdit(tid) {
      // console.log(cid);//获取指定数据的id
      this.axios({
        url: "http://localhost:15850/api/FanTeam",
        method: "get",
        params: {
          tid: tid,
        },
      }).then((res) => {
        console.log(res);
        this.UptForm = res.data;
      });
      this.UptDialogVisible = true; //弹窗
    },
    //修改
    UptUser() {
      console.log(this.UptForm, 4649);//测试
      this.axios({
        url: "http://localhost:15850/api/PutTeam",
        method: "put",
        params: {
          tid: this.UptForm.tid, //反填Id
          trueName: this.UptForm.trueName,
          tsitearea: this.UptForm.tsitearea,
          tsiteSmall: this.UptForm.tsiteSmall,
          tsiteAddress: this.UptForm.tsiteAddress,
          tlongitude: this.UptForm.tlongitude,
          tlatitude: this.UptForm.tlatitude,
          tmap: this.UptForm.tmap,
          tIsGoHome: this.UptForm.tIsGoHome,
          tcost: this.UptForm.tcost,
          talipay: this.UptForm.talipay,
          tbank: this.UptForm.tbank,
          tbankCardPerson: this.UptForm.tbankCardPerson,
          tbankCard: this.UptForm.tbankCard,
          tbankWhere: this.UptForm.tbankWhere,
        },
      }).then((res) => {
        if (res.data > 0) {
          this.$message({
            type: "success",
            message: "修改成功",
            align: "center",
          });
        }
        console.log(res);
        this.UptDialogVisible = false;
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