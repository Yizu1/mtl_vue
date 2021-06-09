<template>
  <div>
    <!-- 面包屑 -->
    <el-breadcrumb separator-class="el-icon-arrow-right">
      <el-breadcrumb-item :to="{ path: '/' }">首页</el-breadcrumb-item>
      <el-breadcrumb-item>门店</el-breadcrumb-item>
      <el-breadcrumb-item>到店支付</el-breadcrumb-item>
    </el-breadcrumb>
    <!-- 卡片视图显示 -->
    <el-card>
      <span style="font-size: 15px">开启到店支付</span>
      <el-switch
        v-model="isNeedCaptcha"
        class="captcha-img"
        active-color="#13ce66"
        inactive-color="#ff4949"
        disabled
        @click.native="changeIsNeedCaptcha(isNeedCaptcha)"
      ></el-switch>
      <br />
      <br />
      <span
        >收款方式
        <!-- `checked` 为 true 或 false -->
        <el-checkbox v-model="isNeedCaptcha">门店扫码</el-checkbox>
      </span>
    </el-card>
  </div>
</template>

<script>
export default {
  name: "switch-test",
  data() {
    return {
      isNeedCaptcha: false,
    };
  },
  methods: {
    // 开启关闭验证
    async changeIsNeedCaptcha(value) {
      this.$confirm(
        `将${!value ? "开启" : "关闭"}到店支付, 是否继续？`,
        "提示",
        {
          confirmButtonText: "确定",
          cancelButtonText: "取消",
          type: "warning",
        }
      )
        .then(async () => {
          this.isNeedCaptcha = !value;
        })
        .catch(() => {
          this.$message.error("取消操作");
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

.captcha-img.el-switch.is-disabled {
  opacity: 1;}
.el-switch__core {
    cursor: pointer;
  }
</style>