<template>
  <div>
    <!-- 面包屑 -->
    <el-breadcrumb separator-class="el-icon-arrow-right">
      <el-breadcrumb-item :to="{ path: '/' }">首页</el-breadcrumb-item>
      <el-breadcrumb-item>门店</el-breadcrumb-item>
      <el-breadcrumb-item>商品</el-breadcrumb-item>
    </el-breadcrumb>
    <!-- 卡片视图显示 -->
    <el-card>
      <el-row :gutter="20">
        <!-- 搜索框 -->
        <el-col :span="8" class="grid">
          <el-input v-model="book.sName" placeholder="ID/名称/货号">
            <el-button
              slot="append"
              icon="el-icon-search"
              @click="getData"
            ></el-button>
          </el-input>
        </el-col>
        <el-col :span="4"></el-col>
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
      <el-table-column prop="sId" label="商品ID"> </el-table-column>
      <el-table-column prop="defPhoto" label="商品主图">
        <!-- 图片操作 -->
        <template slot-scope="scope">
          <img :src="scope.row.defPhoto" style="width: 120px; height: 70px" />
        </template>
      </el-table-column>
      <el-table-column prop="sName" label="商品名称"> </el-table-column>
      <el-table-column prop="sprice" label="价格"> </el-table-column>
      <el-table-column prop="repertory" label="库存"> </el-table-column>
      <el-table-column prop="ssell" label="销量"> </el-table-column>
      <el-table-column prop="attribute" label="属性"> </el-table-column>
      <el-table-column prop="state" label="是否上架">
        <template slot-scope="scope">
          <el-switch
            v-model="scope.row.state"
            :active-value="1"
            :inactive-value="0"
            active-text="上架"
            inactive-text="下架"
            @change="ChangeCstate(scope.$index, scope.row)"
          >
          </el-switch>
        </template>
      </el-table-column>
      <el-table-column prop="sDate" label="创建时间"> </el-table-column>
      <el-table-column prop="" label="操作" width="165px">
        <template slot-scope="scope">
          <!-- 修改按钮 -->
          <el-button
            type="primary"
            icon="el-icon-edit"
            @click="handleEdit(scope.row.sId)"
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
        <el-form-item label="商品名称" prop="sName">
          <el-input
            v-model="UptForm.sName"
            placeholder="请输入商品名称"
          ></el-input>
        </el-form-item>
        <el-form-item label="价格" prop="sprice">
          <el-input
            v-model="UptForm.sprice"
            placeholder="请输入价格"
          ></el-input>
        </el-form-item>
        <el-form-item label="库存" prop="repertory">
          <el-input
            v-model="UptForm.repertory"
            placeholder="请输入库存"
          ></el-input>
        </el-form-item>
        <el-form-item label="销量" prop="ssell">
          <el-input
            v-model="UptForm.ssell"
            placeholder="请输入销量"
          ></el-input>
        </el-form-item>
        <el-form-item label="属性" prop="attribute">
          <el-input
            v-model="UptForm.attribute"
            placeholder="请输入属性"
          ></el-input>
        </el-form-item>
        <el-form-item label="是否上架" prop="state">
          <el-switch
            v-model="UptForm.state"
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
      //查询字段
      book: {
        sName: "",
      },
            //控制修改对话框的显示和隐藏
      UptDialogVisible: false,
      //查询到修改的用户信息对象
      UptForm: {},
      //修改的表单数据
      UptFormRules: {
        sName: [
          { required: true, message: "请输入正确的商品名称", trigger: "blur" },
          {
            min:1,
            max: 10,
            message: "等级名称的长度在1~10个字符之间",
            trigger: "blur",
          },
        ],
        sprice: [
          { required: true, message: "请输入正确的价格", trigger: "blur" },

        ],
        repertory: [
          { required: true, message: "请输入正确的库存", trigger: "blur" },
        ],
        ssell: [
          { required: true, message: "请输入正确的销量", trigger: "blur" },
        ],
         attribute: [
          { required: true, message: "请输入正确的属性", trigger: "blur" },
          {
            min:1,
            max: 10,
            message: "等级名称的长度在1~10个字符之间",
            trigger: "blur",
          },
        ],
      },
      currentPage: 1, //当前页码
      pageSize: 2, //每页的数据条数
      total: 0, //默认总条数为一条

    };
  },
  methods: {
    //显示
    getData() {
      this.axios({
        url: "http://localhost:15850/api/GetGoods",
        method: "get",
        params: {
          sName: this.book.sName,
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
    //删除
    Delete(data) {
      this.$confirm("是否删除?", "提示", {
        confirmButtonText: "确定",
        cancelButtonText: "取消",
        type: "warning",
      }).then(() => {
        this.axios({
          url: "http://localhost:15850/api/DelGoods",
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
        url: "http://localhost:15850/api/UptStates",
        method: "put",
        params: {
          state: row.state,
          sId: row.sId,
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
    handleEdit(sId,row) {
      console.log(sId);//获取指定数据的id
      this.axios({
        url: "http://localhost:15850/api/FanGoods",
        method: "get",
        params: {
          sId: sId,
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
        url: "http://localhost:15850/api/UptGoods",
        method: "put",
        data: {
          sId: this.UptForm.sId,//反填Id
          sName: this.UptForm.sName,
          sprice: this.UptForm.sprice,
          repertory: this.UptForm.repertory,
          ssell: this.UptForm.ssell,
          attribute: this.UptForm.attribute,
          state: this.UptForm.state,
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
.el-switch__label--left {
  position: relative;
  left: 60px;
  color: #fff;
  z-index: -1111;
}
.el-switch__core {
  width: 60px !important;
}
.el-switch__label--right {
  position: relative;
  right: 60px;
  color: #fff;
  z-index: -1111;
}
.el-switch__label--right.is-active {
  z-index: 1111;
  color: #fff !important;
}
.el-switch__label--left.is-active {
  z-index: 1111;
  color: #9c9c9c !important;
}
</style>