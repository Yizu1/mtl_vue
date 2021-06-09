<template>
  <div>
    <!-- 面包屑 -->
    <el-breadcrumb separator-class="el-icon-arrow-right">
      <el-breadcrumb-item :to="{ path: '/' }">首页</el-breadcrumb-item>
      <el-breadcrumb-item>门店</el-breadcrumb-item>
      <el-breadcrumb-item>门店</el-breadcrumb-item>
    </el-breadcrumb>
    <!-- 卡片视图显示 -->
    <el-card>
      <el-row :gutter="20">
        <!-- 按钮 -->
        <el-button type="primary" @click="addDialogVisible = true"
          >新增</el-button
        >
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
      <el-table-column prop="shopID" label="ID"> </el-table-column>
      <el-table-column prop="shopName" label="门店名称"> </el-table-column>
      <el-table-column prop="shopManager" label="门店店长"> </el-table-column>
      <el-table-column prop="goodsID" label="在售商品"> </el-table-column>
      <el-table-column prop="sale" label="销售额"> </el-table-column>
      <el-table-column prop="pickUp" label="是否支持自提">
        <template slot-scope="scope">
          <p v-if="tableData[scope.$index].pickUp == 1" style="color: green">
            自提
          </p>
          <p v-if="tableData[scope.$index].pickUp == 0" style="color: orange">
            不自提
          </p>
        </template>
      </el-table-column>
      <el-table-column prop="type" label="类型">
        <template slot-scope="scope">
          <p v-if="tableData[scope.$index].type == 1">微信端</p>
          <p v-if="tableData[scope.$index].type == 2">支付宝端</p>
          <p v-if="tableData[scope.$index].type == 3">银行卡端</p>
        </template>
      </el-table-column>
      <el-table-column label="状态">
        <template slot-scope="scope">
          <el-switch
            v-model="scope.row.state"
            :active-value="1"
            :inactive-value="0"
            @change="ChangeCstate(scope.$index, scope.row)"
          >
          </el-switch>
        </template>
      </el-table-column>
      <el-table-column prop="" label="操作" width="165px">
        <template slot-scope="scope">
          <!-- 修改按钮 -->
          <el-button
            type="primary"
            icon="el-icon-edit"
            @click="handleEdit(scope.row.shopID)"
          ></el-button>
          <!-- 删除按钮 -->
          <el-button
            type="danger"
            icon="el-icon-delete"
            @click="Delete(scope.row)"
          ></el-button>
        </template>
      </el-table-column>
    </el-table>
    <!-- 新增对话框 -->
    <el-dialog
      title="新增"
      :visible.sync="addDialogVisible"
      width="50%"
      :before-close="addDialogClosed"
    >
      <!-- 内容主体区域 -->
      <el-form
        :model="addForm"
        :rules="addFormRules"
        ref="addForm"
        label-width="110px"
      >
        <el-form-item label="等级名称" prop="cname">
          <el-input
            v-model="addForm.cname"
            placeholder="请输入等级名称"
          ></el-input>
        </el-form-item>
        <el-form-item label="等级经验" prop="cexperience">
          <el-input
            v-model="addForm.cexperience"
            placeholder="请输入等级经验"
          ></el-input>
        </el-form-item>
        <el-form-item label="奖励比例(%)" prop="cratio">
          <el-input
            v-model="addForm.cratio"
            placeholder="请输入奖励比例"
          ></el-input>
        </el-form-item>
        <el-form-item label="状态" prop="cstate">
          <el-switch v-model="addForm.state"></el-switch>
        </el-form-item>
      </el-form>
      <!-- 底部区域 -->
      <span
        slot="footer"
        class="dialog-footer"
        style="display: block; text-align: center"
      >
        <el-button type="primary" @click="addUser">提 交</el-button>
      </span>
    </el-dialog>
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
        :rules="UptFormRules"
        ref="UptFormRef"
        label-width="120px"
      >
        <el-form-item label="登录账号" prop="loginName">
          <el-input
            v-model="UptForm.loginName"
            placeholder="请输入登录账号"
          ></el-input>
        </el-form-item>
        <el-form-item label="密码" prop="loginPwd">
          <el-input
            v-model="UptForm.loginPwd"
            placeholder="请输入密码"
          ></el-input>
        </el-form-item>
        <el-form-item label="门店名称" prop="shopName">
          <el-input
            v-model="UptForm.shopName"
            placeholder="请输入门店名称"
          ></el-input>
        </el-form-item>
        <el-form-item label="门店店长" prop="shopManager">
          <el-input
            v-model="UptForm.shopManager"
            placeholder="请输入门店店长"
          ></el-input>
        </el-form-item>
        <el-form-item label="店长手机号" prop="managerPhone">
          <el-input
            v-model="UptForm.managerPhone"
            placeholder="请输入店长手机号"
          ></el-input>
        </el-form-item>
        <el-form-item label="门店背景" prop="shopBack">
          <el-input
            v-model="UptForm.shopBack"
            placeholder="请输入门店背景"
          ></el-input>
        </el-form-item>
        <el-form-item label="门店LOGO" prop="shopLogo">
          <el-input
            v-model="UptForm.shopLogo"
            placeholder="请输入门店LOGO"
          ></el-input>
        </el-form-item>
        <el-form-item label="认证信息" prop="authen">
          <el-checkbox v-model="checked">实店认证</el-checkbox>
          <el-checkbox v-model="checked">食品经营许可证</el-checkbox>
        </el-form-item>
        <el-form-item label="地址" prop="location">
          <el-input
            v-model="UptForm.location"
            placeholder="请输入地址"
          ></el-input>
        </el-form-item>
        <el-form-item label="详细地址" prop="address">
          <el-input
            v-model="UptForm.address"
            placeholder="请输入详细"
          ></el-input>
        </el-form-item>
        <el-form-item label="自提点地址" prop="selfPickUp">
          <el-input
            v-model="UptForm.selfPickUp"
            placeholder="请输入自提点地址"
          ></el-input>
        </el-form-item>
        <el-form-item label="地图定位" prop="map">
          <el-input
            v-model="UptForm.map"
            placeholder="请输入门店店长"
          ></el-input>
        </el-form-item>
        <el-form-item label="是否开启配送" prop="distrbution">
          <el-switch
            v-model="UptForm.distrbution"
            :active-value="1"
            :inactive-value="0"
          >
          </el-switch>
        </el-form-item>
        <el-form-item label="配送费" prop="delivery">
          <el-input
            v-model="UptForm.delivery"
            placeholder="请输入配送费"
          ></el-input>
        </el-form-item>
        <el-form-item label="提现折扣点(%)" prop="withdraw">
          <el-input
            v-model="UptForm.withdraw"
            placeholder="请输入提现折扣点"
          ></el-input>
        </el-form-item>
        <el-form-item label="简单说明" prop="explain">
          <el-input
            v-model="UptForm.explain"
            placeholder="请输入简单说明"
          ></el-input>
        </el-form-item>
        <el-form-item label="是否营业" prop="open">
          <el-switch
            v-model="UptForm.open"
            :active-value="1"
            :inactive-value="0"
          >
          </el-switch>
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
  </div>
</template>

<script>
export default {
  data() {
    return {
      tableData: [],
      currentPage: 1, //当前页码
      pageSize: 2, //每页的数据条数
      total: 0, //默认总条数为一条
      //控制对话框的显示与隐藏
      addDialogVisible: false,
      //添加用户的表单数据
      addForm: {
        cname: "",
        cexperience: "",
        cratio: "",
        state: false ? 0 : 1,
      },
      //添加表单的验证规则
      addFormRules: {
        cname: [
          { required: true, message: "请输入正确的等级名称", trigger: "blur" },
          {
            max: 4,
            message: "等级名称的长度在3~10个字符之间",
            trigger: "blur",
          },
        ],
        cexperience: [
          { required: true, message: "请输入正确的经验", trigger: "blur" },
        ],
        cratio: [
          { required: true, message: "请输入正确的奖励比例", trigger: "blur" },
        ],
      },
      //控制修改对话框的显示和隐藏
      UptDialogVisible: false,
      //查询到修改的用户信息对象
      UptForm: {},
      //修改的表单数据
      UptFormRules: {
        loginName: [
          { required: true, message: "登录名称不能为空!", trigger: "blur" },
        ],
        shopName: [
          { required: true, message: "门店名称不能为空!", trigger: "blur" },
        ],
        shopManag: [
          { required: true, message: "门店店长不能为空!", trigger: "blur" },
        ],
        managerPhone: [
          { required: true, message: "店长手机号不能为空!", trigger: "blur" },
        ],
        address: [
          { required: true, message: "详细地址不能为空!", trigger: "blur" },
        ],
        selfPickUp: [
          { required: true, message: "自提点地址不能为空!", trigger: "blur" },
        ],
        selfCoord: [
          { required: true, message: "自提点坐标不能为空!", trigger: "blur" },
        ],
        delivery: [
          { required: true, message: "配送费不能为空!", trigger: "blur" },
        ],
        withdraw: [
          { required: true, message: "提现扣点(%)不能为空!", trigger: "blur" },
        ],
        explain: [
          { required: true, message: "简单说明不能为空!", trigger: "blur" },
        ],
      },
    };
  },
  methods: {
    //显示
    getData() {
      this.axios({
        url: "http://localhost:15850/api/GetShop2",
        method: "get",
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
    //监听添加对话框的关闭事件
    addDialogClosed() {
      this.addDialogVisible = false;
      this.getData();
    },
    //添加
    addUser() {
      console.log(this.addForm, 4649);
      this.axios({
        url: "http://localhost:15850/api/AddTeamClass",
        method: "post",
        params: {
          cname: this.addForm.cname,
          cexperience: this.addForm.cexperience,
          cratio: this.addForm.cratio,
          cstate: this.addForm.cstate ? 1 : 0,
        },
      }).then((res) => {
        if (res.data > 0) {
          this.$message({
            type: "success",
            message: "添加成功",
            align: "center",
          });
        }
        console.log(res);
        this.addDialogVisible = false;
        this.getData();
      });
    },
    //删除
    Delete(data) {
      this.$confirm("是否删除?", "提示", {
        confirmButtonText: "确定",
        cancelButtonText: "取消",
        type: "warning",
      }).then(() => {
        this.axios({
          url: "http://localhost:15850/api/DelShop2",
          method: "delete",
          params: {
            shopID: data.shopID,
          },
        }).then((res) => {
          console.log(res);
          if (res.data > 0) {
            this.$message({
              type: "success",
              message: "删除成功",
            });
          } else this.$message.error("删除失败");
          this.getData();
        });
      });
    },
    //开关修改事件
    ChangeCstate(index, row) {
      console.log(row);
      this.axios({
        url: "http://localhost:15850/api/UpdateState",
        method: "put",
        params: {
          state: row.state,
          shopid: row.shopID,
        },
        headers: {
          "Content-Type": "application/json",
        },
      }).then((d) => {
        if (d.data > 0) {
          this.$message({
            message: "恭喜你，修改成功",
            type: "success",
          });
        } else {
          this.$message.error("错了哦，修改失败");
        }
      });
    },
    //监听修改对话框的关闭事件
    UptDialogClosed() {
      this.$refs.UptFormRef.resetFields(); //控制重置
      this.UptDialogVisible = false; //点击X时关闭
    },
    //修改弹窗显示(反填)
    handleEdit(shopID) {
      // console.log(cid);//获取指定数据的id
      this.axios({
        url: "http://localhost:15850/api/FanShop2",
        method: "get",
        params: {
          shopID: shopID,
        },
      }).then((res) => {
        console.log(res);
        this.UptForm = res.data;
      });
      this.UptDialogVisible = true; //弹窗
    },
    //修改
    UptUser() {
      //console.log(this.UptForm, 4649);//测试
      this.axios({
        url: "http://localhost:15850/api/UpdateShop2",
        method: "put",
        params: {
          shopID: this.UptForm.shopID, //反填Id
          loginName: this.UptForm.loginName,
          loginPwd: this.UptForm.loginPwd,
          shopName: this.UptForm.shopName,
          shopManag: this.UptForm.shopManag,
          managerPhone: this.UptForm.managerPhone,
          shopBack: this.UptForm.shopBack,
          shopLogo: this.UptForm.shopLogo,
          authen: this.UptForm.authen,
          location: this.UptForm.location,
          address: this.UptForm.address,
          selfPickUp: this.UptForm.selfPickUp,
          selfCoord: this.UptForm.selfCoord,
          map: this.UptForm.map,
          distrbution: this.UptForm.distrbution,
          delivery: this.UptForm.delivery,
          withdraw: this.UptForm.withdraw,
          explain: this.UptForm.explain,
          open: this.UptForm.open,
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