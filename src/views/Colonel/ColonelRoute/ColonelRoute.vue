<template>
  <!-- 团长路线 -->
  <div>
    <!-- 面包屑 -->
    <el-breadcrumb separator-class="el-icon-arrow-right">
      <el-breadcrumb-item :to="{ path: '/' }">首页</el-breadcrumb-item>
      <el-breadcrumb-item>团长</el-breadcrumb-item>
      <el-breadcrumb-item>路线</el-breadcrumb-item>
    </el-breadcrumb>
    <!-- 卡片视图显示 -->
    <el-card>
      <el-row :gutter="20">
        <el-input
          v-model="book.pname"
          placeholder="请输入名称"
          style="width: 350px"
        ></el-input>
        <el-button type="primary" @click="getData">查询</el-button>
        <!-- 按钮 -->
        <el-button type="primary" @click="addDialogVisible = true"
          >新增</el-button
        >
      </el-row>
    </el-card>
    <!-- 显示的数据 -->
    <el-table :data="tableData" border stripe>
      <el-table-column prop="mname" label="路线"> </el-table-column>
      <el-table-column prop="pname" label="联系人姓名"> </el-table-column>
      <el-table-column prop="pphone" label="联系人电话"> </el-table-column>
      <el-table-column prop="paddress" label="仓库地址"> </el-table-column>
      <el-table-column prop="plal" label="经纬度"></el-table-column>
      <el-table-column prop="pteamcount" label="团长人数"> </el-table-column>
      <el-table-column label="状态">
        <template slot-scope="scope">
          <el-switch
            v-model="scope.row.pstatus"
            :active-value="1"
            :inactive-value="0"
            @change="ChangeCstate(scope.$index, scope.row)"
          ></el-switch>
        </template>
      </el-table-column>
      <el-table-column label="操作">
        <template slot-scope="scope">
          <!-- 地图按钮 -->
          <el-button
            type="primary"
            icon="el-icon-location-outline"
            size="mini"
          ></el-button>
          <!-- 修改按钮 -->
          <el-button
            type="primary"
            icon="el-icon-edit"
            size="mini"
            @click="handleEdit(scope.row.pid, scope.row)"
          ></el-button>
          <!-- 删除按钮 -->
          <el-button
            type="danger"
            icon="el-icon-delete"
            @click="Delete(scope.row)"
            size="mini"
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
        ref="addFormRef"
        label-width="110px"
      >
        <el-form-item label="仓库名称" prop="mid">
          <el-input
            v-model="addForm.mid"
            placeholder="请输入仓库名称"
          ></el-input>
        </el-form-item>
        <el-form-item label="联系人姓名" prop="pname">
          <el-input
            v-model="addForm.pname"
            placeholder="请输入联系人姓名"
          ></el-input>
        </el-form-item>
        <el-form-item label="联系电话" prop="pphone">
          <el-input
            v-model="addForm.pphone"
            placeholder="请输入联系电话"
          ></el-input>
        </el-form-item>
        <el-form-item label="仓库地址" prop="paddress">
          <el-input
            v-model="addForm.paddress"
            placeholder="请输入仓库地址"
          ></el-input>
        </el-form-item>
        <el-form-item label="经纬度" prop="plal">
          <el-input
            v-model="addForm.plal"
            placeholder="请输入经纬度"
          ></el-input>
          <a href="">点击获取坐标</a>
        </el-form-item>
        <el-form-item label="状态" prop="pstatus">
          <el-switch v-model="addForm.pstatus"></el-switch>
        </el-form-item>
      </el-form>
      <!-- 底部区域 -->
      <span
        slot="footer"
        class="dialog-footer"
        style="display: block; text-align: center"
        ><!-- 居中操作 -->
        <el-button type="primary" @click="addUser">提 交</el-button>
      </span>
    </el-dialog>
    <!-- 修改对话框 -->
    <el-dialog
      title="修改用户"
      :visible.sync="UptDialogVisible"
      width="50%"
      :before-close="UptDialogClosed"
    >
      <!-- 内容主体区域 -->
      <el-form
        :model="UptForm"
        :rules="UptFormRules"
        ref="UptFormRef"
        label-width="110px"
      >
        <el-form-item label="仓库名称">
          <el-input
            v-model="UptForm.mname"
            placeholder="请输入仓库名称"
            disabled
          ></el-input>
        </el-form-item>
        <el-form-item label="联系人姓名" prop="pname">
          <el-input
            v-model="UptForm.pname"
            placeholder="请输入联系人姓名"
          ></el-input>
        </el-form-item>
        <el-form-item label="联系电话" prop="pphone">
          <el-input
            v-model="UptForm.pphone"
            placeholder="请输入联系电话"
          ></el-input>
        </el-form-item>
        <el-form-item label="仓库地址" prop="paddress">
          <el-input
            v-model="UptForm.paddress"
            placeholder="请输入仓库地址"
          ></el-input>
        </el-form-item>
        <el-form-item label="经纬度" prop="plal">
          <el-input
            v-model="UptForm.plal"
            placeholder="请输入经纬度"
          ></el-input>
          <a href="">点击获取坐标</a>
        </el-form-item>
        <el-form-item label="状态" prop="pstatus">
          <el-switch
            v-model="UptForm.pstatus"
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
  </div>
</template>

<script>
export default {
  data() {
    return {
      tableData: [], //显示的数据
      book: {
        pname: "",
      },
      //控制对话框的显示与隐藏
      addDialogVisible: false,
      //添加用户的表单数据
      addForm: {
        mid: "",
        pname: "",
        pphone: "",
        paddress: "",
        plal: "",
        pstatus: true,
      },
      //添加表单的验证规则
      addFormRules: {
        mid: [
          { required: true, message: "请输入仓库名称", trigger: "blur" },
          {
            min: 1,
            max: 10,
            message: "用户名的长度在1~10个字符之间",
            trigger: "blur",
          },
        ],
        pname: [
          { required: true, message: "请输入联系人姓名", trigger: "blur" },
          {
            min: 3,
            max: 10,
            message: "联系人姓名的长度在3~10个字符之间",
            trigger: "blur",
          },
        ],
        pphone: [
          {
            required: true,
            pattern: /^1[3-9]\d{9}$/,
            message: "请输入正确联系电话",
            trigger: "blur",
          },
        ],
        paddress: [
          { required: true, message: "请输入仓库地址", trigger: "blur" },
          {
            min: 3,
            max: 10,
            message: "仓库地址的长度在3~10个字符之间",
            trigger: "blur",
          },
        ],
        plal: [
          { required: true, message: "请输入经纬度", trigger: "blur" },
          {
            min: 3,
            max: 20,
            message: "经纬度的长度在3~20个字符之间",
            trigger: "blur",
          },
        ],
      },
      //控制修改对话框的显示和隐藏
      UptDialogVisible: false,
      //查询到修改的用户信息对象
      UptForm: {},
      //修改表单的验证规则
      UptFormRules: {
        mname: [
          { required: true, message: "请输入仓库名称", trigger: "blur" },
          {
            min: 2,
            max: 10,
            message: "用户名的长度在3~10个字符之间",
            trigger: "blur",
          },
        ],
        pname: [
          { required: true, message: "请输入联系人姓名", trigger: "blur" },
          {
            min: 2,
            max: 10,
            message: "联系人姓名的长度在3~10个字符之间",
            trigger: "blur",
          },
        ],
        pphone: [
          {
            required: true,
            pattern: /^1[3-9]\d{9}$/,
            message: "请输入正确联系电话",
            trigger: "blur",
          },
        ],
        paddress: [
          { required: true, message: "请输入仓库地址", trigger: "blur" },
          {
            min: 3,
            max: 10,
            message: "仓库地址的长度在3~10个字符之间",
            trigger: "blur",
          },
        ],
        plal: [
          { required: true, message: "请输入经纬度", trigger: "blur" },
          {
            min: 3,
            max: 20,
            message: "经纬度的长度在3~20个字符之间",
            trigger: "blur",
          },
        ],
      },
    };
  },
  methods: {
    //显示
    getData() {
      this.axios({
        url: "http://localhost:15850/api/GetPath",
        method: "get",
        params: {
          pname: this.book.pname,
        },
      }).then((res) => {
        console.log(res);
        this.tableData = res.data.data;
      });
    },
    //监听添加对话框的关闭事件
    addDialogClosed() {
      this.$refs.addFormRef.resetFields();
      this.addDialogVisible = false;
    },
    //点击按钮，添加数据
    addUser() {
      console.log(this.addForm, 4649); //测试
      //发起添加用户的网络请求
      this.axios({
        url: "http://localhost:15850/api/AddPath",
        method: "post",
        params: {
          mid: this.addForm.mid,
          pname: this.addForm.pname,
          pphone: this.addForm.pphone,
          paddress: this.addForm.paddress,
          plal: this.addForm.plal,
          pstatus: this.addForm.pstatus,
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
          url: "http://localhost:15850/api/DelPath",
          method: "delete",
          params: {
            pid: data.pid,
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
        url: "http://localhost:15850/api/UptPtates",
        method: "put",
        params: {
          pid: row.pid,
          pstatus: row.pstatus,
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
    handleEdit(pid, row) {
      console.log(pid); //获取指定数据的id
      this.axios({
        url: "http://localhost:15850/api/FanPath",
        method: "get",
        params: {
          pid: pid,
        },
      }).then((res) => {
        console.log(res);
        this.UptForm = res.data;
        this.UptForm.mname = row.mname;
      });
      this.UptDialogVisible = true; //弹窗
    },
    //修改
    UptUser() {
      console.log(this.UptForm, 4649);//测试
      this.axios({
        url: "http://localhost:15850/api/UpdatePath",
        method: "put",
        params: {
          pid: this.UptForm.pid, //反填Id
          pname: this.UptForm.pname,
          pphone: this.UptForm.pphone,
          paddress: this.UptForm.paddress,
          plal: this.UptForm.plal,
          pstatus: this.UptForm.pstatus,
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
    this.getData(); //显示
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
.el-table {
  margin-top: 15px;
  padding-right: 2px;
}
.el-input {
  width: 650px;
}
span {
  text-align: center;
}
.el-button {
  margin-left: 15px;
}
</style>