<template>
  <!-- 设置 -->
  <div>
    <!-- 面包屑 -->
    <el-breadcrumb separator-class="el-icon-arrow-right">
      <el-breadcrumb-item :to="{ path: '/' }">首页</el-breadcrumb-item>
      <el-breadcrumb-item>团长</el-breadcrumb-item>
      <el-breadcrumb-item>设置</el-breadcrumb-item>
    </el-breadcrumb>
    <!-- 卡片视图显示 -->
    <el-card>
      <span>团购配置</span>
      <!-- 数据的提交 -->
    </el-card>
    <div border style="padding-left: 600px;">
      <!-- 内容主体区域 -->
      <el-form
        :model="addForm"
        :rules="addFormRules"
        ref="addFormRef"
        label-width="110px"
      >
        <el-form-item label="开启团购" prop="biscan">
          <el-switch v-model="addForm.biscan"></el-switch>
        </el-form-item>
        <el-form-item label="公告" prop="bnotice">
          <el-input
            v-model="addForm.bnotice"
            placeholder="请输入公告"
          ></el-input>
        </el-form-item>
        <el-form-item label="休市时间" prop="cratio">
          <div class="block">
            <el-date-picker
              v-model="value1"
              type="datetime"
              placeholder="选择开始时间"
            >
            </el-date-picker>
            <span>至</span>
             <el-date-picker
              v-model="value1"
              type="datetime"
              placeholder="选择结束时间"
            >
            </el-date-picker>
          </div>
        </el-form-item>
        <el-form-item label="团长申请海报" prop="bpleaseimg">
          <el-upload
            class="upload-demo"
            action="https://jsonplaceholder.typicode.com/posts/"
            :on-preview="handlePreview"
            :on-remove="handleRemove"
            :before-remove="beforeRemove"
            multiple
            :limit="3"
            :on-exceed="handleExceed"
            :file-list="fileList"
          >
            <el-button size="small" type="primary">点击上传</el-button>
          </el-upload>
        </el-form-item>
        <el-form-item label="休市海报" prop="bcloseimg">
          <el-upload
            class="upload-demo"
            action="https://jsonplaceholder.typicode.com/posts/"
            :on-preview="handlePreview"
            :on-remove="handleRemove"
            :before-remove="beforeRemove"
            multiple
            :limit="3"
            :on-exceed="handleExceed"
            :file-list="fileList"
          >
            <el-button size="small" type="primary">点击上传</el-button>
          </el-upload>
        </el-form-item>
        <el-form-item label="供应商申请海报" prop="bsupplierimg">
          <el-upload
            class="upload-demo"
            action="https://jsonplaceholder.typicode.com/posts/"
            :on-preview="handlePreview"
            :on-remove="handleRemove"
            :before-remove="beforeRemove"
            multiple
            :limit="3"
            :on-exceed="handleExceed"
            :file-list="fileList"
          >
            <el-button size="small" type="primary">点击上传</el-button>
          </el-upload>
        </el-form-item>
        <el-form-item label="发货方式" prop="bways">
          <el-checkbox v-model="checked">快递</el-checkbox>
          <el-checkbox v-model="checked">自提</el-checkbox>
          <el-checkbox v-model="checked">团长配送</el-checkbox>
        </el-form-item>
        <el-form-item label="自定义团长名" prop="tid">
          <el-input
            v-model="addForm.tid"
            placeholder="请输入自定义团长名"
          ></el-input>
        </el-form-item>
        <el-form-item label="团长覆盖范围" prop="bscope">
          <el-input
            v-model="addForm.bscope"
            placeholder="请输入团长覆盖范围"
          ></el-input>
          <span>公里</span>
        </el-form-item>
        <el-form-item label="提现手续费" prop="bTkmoney">
          <el-input
            v-model="addForm.bTkmoney"
            placeholder="请输入提现手续费"
          ></el-input>
          <span>%</span>
        </el-form-item>
        <el-form-item label="最低提现金额" prop="blowTkmoney">
          <el-input
            v-model="addForm.blowTkmoney"
            placeholder="请输入最低提现金额"
          ></el-input>
          <span>最低一元</span>
        </el-form-item>
        <el-form-item label="团长佣金" prop="bteammoney">
          <el-input
            v-model="addForm.bteammoney"
            placeholder="请输入团长佣金"
          ></el-input>
          <span>%</span>
        </el-form-item>
      </el-form>
      <!-- 底部区域 -->
      <span
        slot="footer"
        class="dialog-footer"
        style="display: block;padding-left:200px;padding-top:20px"
      >
        <el-button type="primary" @click="addUser">提 交</el-button>
      </span>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      //添加字段
      addForm: {
        biscan: true,
        bnotice: "",
        bbegintime: "",
        bendtime: "",
        bpleaseimg: "",
        bcloseimg: "",
        bsupplierimg: "",
        bways: "",
        bscope: "",
        bTkmoney: "",
        blowTkmoney: "",
        bteammoney: "",
        tid: "",
      },
      //添加表单的验证规则
      addFormRules: {
        bnotice: [
          { required: true, message: "公告不能为空!", trigger: "blur" },
        ],
        tid: [
          { required: true, message: "团长名不能为空!", trigger: "blur" },
        ],
        bscope: [
          { required: true, message: "团长覆盖范围不能为空!", trigger: "blur" },
        ],
        bTkmoney: [
          { required: true, message: "提现手续费不能为空!", trigger: "blur" },
        ],
        blowTkmoney: [
          { required: true, message: "最低提现金额不能为空!", trigger: "blur" },
          {
            pattern:/^(0|-?[1-9][0-9]*)$/,
            message: "提现手续费最低一元",
            trigger: "blur",
          },
        ],
        bteammoney: [
          { required: true, message: "团长佣金不能为空!", trigger: "blur" },
        ],
      },
    };
  },
  methods: {
    addUser() {
      console.log(this.addForm, 4649); //测试
      this.axios({
        url: "http://localhost:15850api/AddTeamBuy",
        method: "post",
        params: {
          biscan: this.addForm.biscan,
          bnotice: this.addForm.bnotice,
          bbegintime: this.addForm.bbegintime,
          bendtime: this.addForm.bendtime,
          bpleaseimg: this.addForm.bpleaseimg,
          bcloseimg: this.addForm.bcloseimg,
          bsupplierimg: this.addForm.bsupplierimg,
          bways: this.addForm.bways,
          bscope: this.addForm.bscope,
          bTkmoney: this.addForm.bTkmoney,
          blowTkmoney: this.addForm.blowTkmoney,
          bteammoney: this.addForm.bteammoney,
          tid: this.addForm.tid,
        },
      }).then((res) => {
        if (res.data > 0) {
          this.$message({
            type: "success",
            message: "设置成功",
            align: "center",
          });
        }
        console.log(res);
        this.addDialogVisible = false;
        this.getData();
      });
    },
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
.el-input {
  width: 800px;
}
</style>