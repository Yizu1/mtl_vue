<template>
  <div>
    <!-- 面包屑 -->
    <el-breadcrumb separator-class="el-icon-arrow-right">
      <el-breadcrumb-item :to="{ path: '/' }">首页</el-breadcrumb-item>
      <el-breadcrumb-item>门店</el-breadcrumb-item>
      <el-breadcrumb-item>订单</el-breadcrumb-item>
    </el-breadcrumb>
    <!-- 卡片 -->
    <el-card class="box-card">
      <el-select
        v-model="book.ts"
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
      <el-input
        v-model="book.aa"
        placeholder="请输入"
        style="width: 200px; margin-left: 12px; margin-right: 12px"
      ></el-input>
      <el-button type="primary" @click="handleClick()">搜索</el-button>
    </el-card>
    <div>
      <!-- 显示全部 -->
      <el-tabs v-model="activeName" @tab-click="handleClick()">
        <el-tab-pane label="全部" name="0"></el-tab-pane>
        <el-tab-pane label="待付款" name="1"></el-tab-pane>
        <el-tab-pane label="待发货" name="2"></el-tab-pane>
        <el-tab-pane label="已发货" name="3"></el-tab-pane>
        <el-tab-pane label="已完成" name="4"></el-tab-pane>
        <el-tab-pane label="退款中" name="5"></el-tab-pane>
        <el-tab-pane label="关闭" name="6"></el-tab-pane>
      </el-tabs>
    </div>
    <!-- 全部 -->
    <div v-if="activeName == 0">
      <!-- 显示的内容 -->
      <el-table :data="tableData" border stripe>
        <el-table-column prop="orderId" label="ID"> </el-table-column>
        <el-table-column label="商家信息">
          <template slot-scope="scope" style="text-align: left">
            <h5>
              昵称：
              <span>{{ scope.row.shopName }}</span>
            </h5>
            <h5>
              姓名：
              <span>{{ scope.row.shopManager }}</span>
            </h5>
            <h5>
              电话：
              <span>{{ scope.row.managerPhone }}</span>
            </h5>
            <h5>
              地址：
              <span>{{ scope.row.address }}</span>
            </h5>
          </template>
        </el-table-column>
        <el-table-column label="买家信息">
          <template slot-scope="scope">
            <h5>
              昵称：
              <span>{{ scope.row.account }}</span>
            </h5>
            <h5>
              姓名：
              <span>{{ scope.row.uname }}</span>
            </h5>
            <h5>
              电话：
              <span>{{ scope.row.uphone }}</span>
            </h5>
            <h5>
              地址：
              <span>{{ scope.row.address }}</span>
            </h5>
          </template>
        </el-table-column>
        <el-table-column prop="shippingMay" label="送货方式"> </el-table-column>
        <el-table-column prop="ccreateTime" label="支付金额">
          <template slot-scope="scope">
            <h5>
              商品总价：
              <span>1.00</span>
            </h5>
            <h5>
              配送费用：
              <span>{{ scope.row.delivery }}.00</span>
            </h5>
            <h5>
              订单总价：
              <span>1.00</span>
            </h5>
            <h5>
              总优惠：
              <span>{{ scope.row.goodPrefer }}.00</span>
            </h5>
            <h5>
              实付金额：
              <span>￥1.00</span>
            </h5>
          </template>
        </el-table-column>
        <el-table-column label="状态" prop="state">
          <template slot-scope="scope">
            <p v-if="tableData[scope.$index].state == 1" style="color: green">
              待付款
            </p>

            <p v-if="tableData[scope.$index].state == 2" style="color: orange">
              待发货
            </p>
            <p
              v-if="tableData[scope.$index].tauditState == 3"
              style="color: red"
            >
              已发货
            </p>
            <p
              v-if="tableData[scope.$index].tauditState == 4"
              style="color: red"
            >
              已完成
            </p>
            <p
              v-if="tableData[scope.$index].tauditState == 5"
              style="color: red"
            >
              退款中
            </p>
            <p
              v-if="tableData[scope.$index].tauditState == 6"
              style="color: red"
            >
              关闭
            </p>
          </template>
        </el-table-column>
      </el-table>
    </div>
    <!-- 待付款 -->
    <div v-if="activeName == 1">
      <!-- 显示的内容 -->
      <el-table :data="tableData" border stripe>
        <el-table-column prop="orderId" label="ID"> </el-table-column>
        <el-table-column label="商家信息">
          <template slot-scope="scope" style="text-align: left">
            <h5>
              昵称：
              <span>{{ scope.row.shopName }}</span>
            </h5>
            <h5>
              姓名：
              <span>{{ scope.row.shopManager }}</span>
            </h5>
            <h5>
              电话：
              <span>{{ scope.row.managerPhone }}</span>
            </h5>
            <h5>
              地址：
              <span>{{ scope.row.address }}</span>
            </h5>
          </template>
        </el-table-column>
        <el-table-column label="买家信息">
          <template slot-scope="scope">
            <h5>
              昵称：
              <span>{{ scope.row.account }}</span>
            </h5>
            <h5>
              姓名：
              <span>{{ scope.row.uname }}</span>
            </h5>
            <h5>
              电话：
              <span>{{ scope.row.uphone }}</span>
            </h5>
            <h5>
              地址：
              <span>{{ scope.row.address }}</span>
            </h5>
          </template>
        </el-table-column>
        <el-table-column prop="shippingMay" label="送货方式"> </el-table-column>
        <el-table-column prop="ccreateTime" label="支付金额">
          <template slot-scope="scope">
            <h5>
              商品总价：
              <span>1.00</span>
            </h5>
            <h5>
              配送费用：
              <span>{{ scope.row.delivery }}.00</span>
            </h5>
            <h5>
              订单总价：
              <span>1.00</span>
            </h5>
            <h5>
              总优惠：
              <span>{{ scope.row.goodPrefer }}.00</span>
            </h5>
            <h5>
              实付金额：
              <span>￥1.00</span>
            </h5>
          </template>
        </el-table-column>
        <el-table-column label="状态" prop="state">
          <template slot-scope="scope">
            <p v-if="tableData[scope.$index].state == 1" style="color: green">
              待付款
            </p>

            <p v-if="tableData[scope.$index].state == 2" style="color: orange">
              待发货
            </p>
            <p
              v-if="tableData[scope.$index].tauditState == 3"
              style="color: red"
            >
              已发货
            </p>
            <p
              v-if="tableData[scope.$index].tauditState == 4"
              style="color: red"
            >
              已完成
            </p>
            <p
              v-if="tableData[scope.$index].tauditState == 5"
              style="color: red"
            >
              退款中
            </p>
            <p
              v-if="tableData[scope.$index].tauditState == 6"
              style="color: red"
            >
              关闭
            </p>
          </template>
        </el-table-column>
      </el-table>
    </div>
    <!-- 待发货 -->
    <div v-if="activeName == 2">
      <!-- 显示的内容 -->
      <el-table :data="tableData" border stripe>
        <el-table-column prop="orderId" label="ID"> </el-table-column>
        <el-table-column label="商家信息">
          <template slot-scope="scope" style="text-align: left">
            <h5>
              昵称：
              <span>{{ scope.row.shopName }}</span>
            </h5>
            <h5>
              姓名：
              <span>{{ scope.row.shopManager }}</span>
            </h5>
            <h5>
              电话：
              <span>{{ scope.row.managerPhone }}</span>
            </h5>
            <h5>
              地址：
              <span>{{ scope.row.address }}</span>
            </h5>
          </template>
        </el-table-column>
        <el-table-column label="买家信息">
          <template slot-scope="scope">
            <h5>
              昵称：
              <span>{{ scope.row.account }}</span>
            </h5>
            <h5>
              姓名：
              <span>{{ scope.row.uname }}</span>
            </h5>
            <h5>
              电话：
              <span>{{ scope.row.uphone }}</span>
            </h5>
            <h5>
              地址：
              <span>{{ scope.row.address }}</span>
            </h5>
          </template>
        </el-table-column>
        <el-table-column prop="shippingMay" label="送货方式"> </el-table-column>
        <el-table-column prop="ccreateTime" label="支付金额">
          <template slot-scope="scope">
            <h5>
              商品总价：
              <span>1.00</span>
            </h5>
            <h5>
              配送费用：
              <span>{{ scope.row.delivery }}.00</span>
            </h5>
            <h5>
              订单总价：
              <span>1.00</span>
            </h5>
            <h5>
              总优惠：
              <span>{{ scope.row.goodPrefer }}.00</span>
            </h5>
            <h5>
              实付金额：
              <span>￥1.00</span>
            </h5>
          </template>
        </el-table-column>
        <el-table-column label="状态" prop="state">
          <template slot-scope="scope">
            <p v-if="tableData[scope.$index].state == 1" style="color: green">
              待付款
            </p>

            <p v-if="tableData[scope.$index].state == 2" style="color: orange">
              待发货
            </p>
            <p
              v-if="tableData[scope.$index].tauditState == 3"
              style="color: red"
            >
              已发货
            </p>
            <p
              v-if="tableData[scope.$index].tauditState == 4"
              style="color: red"
            >
              已完成
            </p>
            <p
              v-if="tableData[scope.$index].tauditState == 5"
              style="color: red"
            >
              退款中
            </p>
            <p
              v-if="tableData[scope.$index].tauditState == 6"
              style="color: red"
            >
              关闭
            </p>
          </template>
        </el-table-column>
      </el-table>
    </div>
    <!-- 已发货 -->
    <div v-if="activeName == 3">
      <!-- 显示的内容 -->
      <el-table :data="tableData" border stripe>
        <el-table-column prop="orderId" label="ID"> </el-table-column>
        <el-table-column label="商家信息">
          <template slot-scope="scope" style="text-align: left">
            <h5>
              昵称：
              <span>{{ scope.row.shopName }}</span>
            </h5>
            <h5>
              姓名：
              <span>{{ scope.row.shopManager }}</span>
            </h5>
            <h5>
              电话：
              <span>{{ scope.row.managerPhone }}</span>
            </h5>
            <h5>
              地址：
              <span>{{ scope.row.address }}</span>
            </h5>
          </template>
        </el-table-column>
        <el-table-column label="买家信息">
          <template slot-scope="scope">
            <h5>
              昵称：
              <span>{{ scope.row.account }}</span>
            </h5>
            <h5>
              姓名：
              <span>{{ scope.row.uname }}</span>
            </h5>
            <h5>
              电话：
              <span>{{ scope.row.uphone }}</span>
            </h5>
            <h5>
              地址：
              <span>{{ scope.row.address }}</span>
            </h5>
          </template>
        </el-table-column>
        <el-table-column prop="shippingMay" label="送货方式"> </el-table-column>
        <el-table-column prop="ccreateTime" label="支付金额">
          <template slot-scope="scope">
            <h5>
              商品总价：
              <span>1.00</span>
            </h5>
            <h5>
              配送费用：
              <span>{{ scope.row.delivery }}.00</span>
            </h5>
            <h5>
              订单总价：
              <span>1.00</span>
            </h5>
            <h5>
              总优惠：
              <span>{{ scope.row.goodPrefer }}.00</span>
            </h5>
            <h5>
              实付金额：
              <span>￥1.00</span>
            </h5>
          </template>
        </el-table-column>
        <el-table-column label="状态" prop="state">
          <template slot-scope="scope">
            <p v-if="tableData[scope.$index].state == 1" style="color: green">
              待付款
            </p>

            <p v-if="tableData[scope.$index].state == 2" style="color: orange">
              待发货
            </p>
            <p
              v-if="tableData[scope.$index].tauditState == 3"
              style="color: red"
            >
              已发货
            </p>
            <p
              v-if="tableData[scope.$index].tauditState == 4"
              style="color: red"
            >
              已完成
            </p>
            <p
              v-if="tableData[scope.$index].tauditState == 5"
              style="color: red"
            >
              退款中
            </p>
            <p
              v-if="tableData[scope.$index].tauditState == 6"
              style="color: red"
            >
              关闭
            </p>
          </template>
        </el-table-column>
      </el-table>
    </div>
    <!-- 已完成 -->
    <div v-if="activeName == 4">
      <!-- 显示的内容 -->
      <el-table :data="tableData" border stripe>
        <el-table-column prop="orderId" label="ID"> </el-table-column>
        <el-table-column label="商家信息">
          <template slot-scope="scope" style="text-align: left">
            <h5>
              昵称：
              <span>{{ scope.row.shopName }}</span>
            </h5>
            <h5>
              姓名：
              <span>{{ scope.row.shopManager }}</span>
            </h5>
            <h5>
              电话：
              <span>{{ scope.row.managerPhone }}</span>
            </h5>
            <h5>
              地址：
              <span>{{ scope.row.address }}</span>
            </h5>
          </template>
        </el-table-column>
        <el-table-column label="买家信息">
          <template slot-scope="scope">
            <h5>
              昵称：
              <span>{{ scope.row.account }}</span>
            </h5>
            <h5>
              姓名：
              <span>{{ scope.row.uname }}</span>
            </h5>
            <h5>
              电话：
              <span>{{ scope.row.uphone }}</span>
            </h5>
            <h5>
              地址：
              <span>{{ scope.row.address }}</span>
            </h5>
          </template>
        </el-table-column>
        <el-table-column prop="shippingMay" label="送货方式"> </el-table-column>
        <el-table-column prop="ccreateTime" label="支付金额">
          <template slot-scope="scope">
            <h5>
              商品总价：
              <span>1.00</span>
            </h5>
            <h5>
              配送费用：
              <span>{{ scope.row.delivery }}.00</span>
            </h5>
            <h5>
              订单总价：
              <span>1.00</span>
            </h5>
            <h5>
              总优惠：
              <span>{{ scope.row.goodPrefer }}.00</span>
            </h5>
            <h5>
              实付金额：
              <span>￥1.00</span>
            </h5>
          </template>
        </el-table-column>
        <el-table-column label="状态" prop="state">
          <template slot-scope="scope">
            <p v-if="tableData[scope.$index].state == 1" style="color: green">
              待付款
            </p>

            <p v-if="tableData[scope.$index].state == 2" style="color: orange">
              待发货
            </p>
            <p
              v-if="tableData[scope.$index].tauditState == 3"
              style="color: red"
            >
              已发货
            </p>
            <p
              v-if="tableData[scope.$index].tauditState == 4"
              style="color: red"
            >
              已完成
            </p>
            <p
              v-if="tableData[scope.$index].tauditState == 5"
              style="color: red"
            >
              退款中
            </p>
            <p
              v-if="tableData[scope.$index].tauditState == 6"
              style="color: red"
            >
              关闭
            </p>
          </template>
        </el-table-column>
      </el-table>
    </div>
    <!-- 退款中 -->
    <div v-if="activeName == 5">
      <!-- 显示的内容 -->
      <el-table :data="tableData" border stripe>
        <el-table-column prop="orderId" label="ID"> </el-table-column>
        <el-table-column label="商家信息">
          <template slot-scope="scope" style="text-align: left">
            <h5>
              昵称：
              <span>{{ scope.row.shopName }}</span>
            </h5>
            <h5>
              姓名：
              <span>{{ scope.row.shopManager }}</span>
            </h5>
            <h5>
              电话：
              <span>{{ scope.row.managerPhone }}</span>
            </h5>
            <h5>
              地址：
              <span>{{ scope.row.address }}</span>
            </h5>
          </template>
        </el-table-column>
        <el-table-column label="买家信息">
          <template slot-scope="scope">
            <h5>
              昵称：
              <span>{{ scope.row.account }}</span>
            </h5>
            <h5>
              姓名：
              <span>{{ scope.row.uname }}</span>
            </h5>
            <h5>
              电话：
              <span>{{ scope.row.uphone }}</span>
            </h5>
            <h5>
              地址：
              <span>{{ scope.row.address }}</span>
            </h5>
          </template>
        </el-table-column>
        <el-table-column prop="shippingMay" label="送货方式"> </el-table-column>
        <el-table-column prop="ccreateTime" label="支付金额">
          <template slot-scope="scope">
            <h5>
              商品总价：
              <span>1.00</span>
            </h5>
            <h5>
              配送费用：
              <span>{{ scope.row.delivery }}.00</span>
            </h5>
            <h5>
              订单总价：
              <span>1.00</span>
            </h5>
            <h5>
              总优惠：
              <span>{{ scope.row.goodPrefer }}.00</span>
            </h5>
            <h5>
              实付金额：
              <span>￥1.00</span>
            </h5>
          </template>
        </el-table-column>
        <el-table-column label="状态" prop="state">
          <template slot-scope="scope">
            <p v-if="tableData[scope.$index].state == 1" style="color: green">
              待付款
            </p>

            <p v-if="tableData[scope.$index].state == 2" style="color: orange">
              待发货
            </p>
            <p
              v-if="tableData[scope.$index].tauditState == 3"
              style="color: red"
            >
              已发货
            </p>
            <p
              v-if="tableData[scope.$index].tauditState == 4"
              style="color: red"
            >
              已完成
            </p>
            <p
              v-if="tableData[scope.$index].tauditState == 5"
              style="color: red"
            >
              退款中
            </p>
            <p
              v-if="tableData[scope.$index].tauditState == 6"
              style="color: red"
            >
              关闭
            </p>
          </template>
        </el-table-column>
      </el-table>
    </div>
    <!-- 关闭 -->
    <div v-if="activeName == 6"></div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      tableData: [],
      //查询字段集合
      book: {
        aa: "",
        ts: "",
      },
      //初始化下拉(tid、name为随意起的名称)
      options: [
        { tid: 1, name: "订单号" },
        { tid: 2, name: "买家姓名" },
        { tid: 3, name: "买家电话" },
      ],
      //导航查询默认选中
      activeName: 0,
    };
  },
  methods: {
    handleClick() {
      //console.log(this.activeName);
      this.axios({
        url: "http://localhost:15850/api/GetOrderD",
        method: "get",
        params: {
          state: this.activeName,
          aa: this.book.aa,
          ts: this.book.ts,
        },
      }).then((res) => {
        console.log(res);
        this.tableData = res.data.data;
      });
    },
  },
  mounted() {
    this.handleClick();
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
.el-option {
  width: 20px;
}
.el-autocomplete {
  padding-left: 20px;
  padding-right: 20px;
}
.el-table td,
.el-table th {
  margin-top: 15px;
  text-align: center !important;
}
</style>