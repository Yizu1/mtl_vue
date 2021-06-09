<template>
  <!-- 团长等级 -->
  <div>
    <!-- 面包屑 -->
    <el-breadcrumb separator-class="el-icon-arrow-right">
      <el-breadcrumb-item :to="{ path: '/' }">首页</el-breadcrumb-item>
      <el-breadcrumb-item>团长</el-breadcrumb-item>
      <el-breadcrumb-item>团长等级</el-breadcrumb-item>
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
    <!-- 显示的内容 -->
    <el-table :data="tableData" border stripe>
      <el-table-column prop="cname" label="等级名称"> </el-table-column>
      <el-table-column prop="cexperience" label="等级经验"> </el-table-column>
      <el-table-column prop="cratio" label="奖励比例(%)"> </el-table-column>
      <el-table-column prop="ccreateTime" label="创建时间"> </el-table-column>
      <el-table-column label="状态">
        <template slot-scope="scope">
          <el-switch
            v-model="scope.row.cstate"
            :active-value="1"
            :inactive-value="0"
            @change="ChangeCstate(scope.$index, scope.row)"
          >
          </el-switch>
        </template>
      </el-table-column>
      <el-table-column prop="" label="操作">
        <template slot-scope="scope">
          <!-- 修改按钮 -->
          <el-button
            type="primary"
            icon="el-icon-edit"
            @click="handleEdit(scope.row.cid)"
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
        ref="addFormRef"
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
          <el-switch v-model="addForm.cstate"></el-switch>
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
      title="修改"
      :visible.sync="UptDialogVisible"
      width="50%"
      :before-close="UptDialogClosed"
    >
      <!-- 修改内容主体区域 -->
      <el-form
        :model="UptForm"
        :rules="UptFormRules"
        ref="UptFormRef"
        label-width="110px"
      >
        <el-form-item label="等级名称" prop="cname">
          <el-input
            v-model="UptForm.cname"
            placeholder="请输入等级名称"
          ></el-input>
        </el-form-item>
        <el-form-item label="等级经验" prop="cexperience">
          <el-input
            v-model="UptForm.cexperience"
            placeholder="请输入等级经验"
          ></el-input>
        </el-form-item>
        <el-form-item label="奖励比例(%)" prop="cratio">
          <el-input
            v-model="UptForm.cratio"
            placeholder="请输入奖励比例"
          ></el-input>
        </el-form-item>
        <el-form-item label="状态" prop="cstate">
          <el-switch
            v-model="UptForm.cstate"
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
      tableData: [],
      //控制对话框的显示与隐藏
      addDialogVisible: false,
      //添加的表单数据
      addForm: {
        cname: "",
        cexperience: "",
        cratio: "",
        cstate: false ? 0 : 1,
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
    };
  },
  methods: {
    //显示
    getData() {
      this.axios({
        url: "http://localhost:15850/api/GetTeamClass",
        method: "get",
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
    //添加
    addUser() {
      console.log(this.addForm, 4649); //测试
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
          url: "http://localhost:15850/api/DelTeamClass",
          method: "post",
          params: {
            cid: data.cid,
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
        url: "http://localhost:15850/api/UptCstate",
        method: "put",
        params: {
          cid: row.cid,
          cstate: row.cstate,
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
      this.$refs.UptFormRef.resetFields();//控制重置
      this.UptDialogVisible = false; //点击X时关闭
    },
    //修改弹窗显示(反填)
    handleEdit(cid) {
      // console.log(cid);//获取指定数据的id
      this.axios({
        url: "http://localhost:15850/api/FanTeamClass",
        method: "get",
        params: {
          cid: cid,
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
        url: "http://localhost:15850/api/UpdateTeamClass",
        method: "put",
        params: {
          cid: this.UptForm.cid,//反填Id
          cname: this.UptForm.cname,
          cexperience: this.UptForm.cexperience,
          cratio: this.UptForm.cratio,
          cstate: this.UptForm.cstate,
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
}
</style>