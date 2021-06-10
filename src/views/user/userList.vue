<template>
  <div class="app-container">
    <!-- 头部筛选框 -->
    <div class="seeks">
      <!-- 筛选条件 -->
      <div class="each">
        <el-input
          v-model="seek.name"
          size="small"
          placeholder="请输入关键字"
          style="width: auto"
        ></el-input>
      </div>
      <!-- 查询和重置按钮 -->
      <div class="each">
        <el-button type="primary" size="small" @click="clickSearch">
          查询</el-button
        >
        <el-button class="" size="small" @click="clickReset">重置</el-button>
      </div>
      <div class="each">
        <el-button type="primary" @click="openAdd">新增设计师</el-button>
      </div>
    </div>

    <!-- 表格部分 -->
    <el-table :data="tableData" style="width: 100%">
      <el-table-column prop="date" label="日期" width="180"> </el-table-column>
      <el-table-column prop="name" label="姓名" width="180"> </el-table-column>
      <el-table-column prop="address" label="地址"> </el-table-column>
    </el-table>

    <!-- 添加和编辑弹窗 -->
    <el-dialog
      :title="maskType == 0 ? '新增商户' : '修改商户'"
      height="200px"
      width="500px"
      :visible.sync="subMaskShow"
      :close-on-click-modal="false"
    >
      <el-form :model="form" :rules="rules" ref="ruleForm" label-width="120px">
        <!-- 设计师名称 -->
        <el-form-item label="设计师名称" prop="merchants">
          <el-input
            v-model="form.merchants"
            style="width: 300px"
            placeholder="请输入设计师名称"
          ></el-input>
        </el-form-item>
        <!-- 电话号码 -->
        <el-form-item label="电话号码" prop="phoneNumber">
          <el-input
            v-model="form.phoneNumber"
            style="width: 300px"
            placeholder="请输入电话号码"
            type="number"
          ></el-input>
        </el-form-item>
        <!-- 手机号码 -->
        <el-form-item label="手机号码" prop="mobileNumber">
          <el-input
            v-model="form.mobileNumber"
            style="width: 300px"
            placeholder="请输入手机号码"
            type="number"
          ></el-input>
        </el-form-item>
        <!-- 简介 -->
        <el-form-item label="简介" prop="tradeAddr">
          <el-input
            v-model="form.tradeAddr"
            style="width: 300px"
            placeholder="请输入简介"
          ></el-input>
        </el-form-item>
        <!-- 企业介绍 -->
        <el-form-item label="介绍" prop="desc">
          <WangEditor v-model="form.desc" :isClear="false" />
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button :loading="maskLoading" @click="subMaskShow = false"
          >取 消</el-button
        >
        <el-button
          type="primary"
          :loading="maskLoading"
          @click="clickMaskSubmit"
          >确 定</el-button
        >
      </div>
    </el-dialog>
  </div>
</template>

<script>
export default {
  data() {
    return {
      // 搜索框
      seek: {
        name: "",
      },

      // 表格数据
      tableData: [
        {
          date: "2016-05-02",
          name: "王小虎",
          address: "上海市普陀区金沙江路 1518 弄",
        },
        {
          date: "2016-05-04",
          name: "王小虎",
          address: "上海市普陀区金沙江路 1517 弄",
        },
        {
          date: "2016-05-01",
          name: "王小虎",
          address: "上海市普陀区金沙江路 1519 弄",
        },
        {
          date: "2016-05-03",
          name: "王小虎",
          address: "上海市普陀区金沙江路 1516 弄",
        },
      ],

      // 弹窗数据
      maskType: 0, // 弹窗类型
      subMaskShow: false, // 是否显示弹窗
      maskLoading: false, // 点击确定 防止多次点击
      form: {}, // 弹窗输入内容
      // 验证规则
      rules: {
        merchants: [
          { required: true, message: "请输入设计师名称", trigger: "blue" },
        ],
        phoneNumber: [
          { required: true, message: "请输入电话号码", trigger: "blue" },
        ],
        mobileNumber: [
          {
            required: true,
            message: "请输入手机号码",
            trigger: "blue",
          },
        ],
        tradeAddr: [{ required: true, message: "请输入简介", trigger: "blue" }],
        desc: [{ required: true, message: "请输入介绍", trigger: "blue" }],
      },
    };
  },
  methods: {
    // 查询按钮
    clickSearch() {},

    // 重置按钮
    clickReset() {},

    // 打开新增弹窗
    openAdd() {
      this.maskType = 0;
      this.subMaskShow = true;
    },

    // 新增弹窗确定按钮
    clickMaskSubmit() {},
  },
};
</script>

<style scoped lang="scss">
.line {
  text-align: center;
}
.seeks {
  display: flex;
  justify-content: start;
  align-items: center;
  margin-bottom: 20px;
  .each {
    display: flex;
    align-items: center;
    margin-right: 20px;
  }
}
</style>

