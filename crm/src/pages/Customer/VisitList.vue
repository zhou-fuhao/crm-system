<template>
  <div class="box" id="box">
    <div class="filterBox">
      <el-button type="primary" disabled @click="dialogFormVisible = true">新增回访记录</el-button>
    </div>

    <el-dialog title="新增回访记录" :visible.sync="dialogFormVisible">
      <el-form :model="visitForm" ref="visitForm">
        <el-form-item label="回访日期" prop="visitTime" :label-width="formLabelWidth">
          <el-date-picker v-model="visitForm.visitTime" type="date" placeholder="选择日期"></el-date-picker>
        </el-form-item>
        <el-form-item label="回访纪要" prop="visitText" :label-width="formLabelWidth">
          <el-input type="textarea" v-model="visitForm.visitText" autocomplete="off"></el-input>
        </el-form-item>
      </el-form>

      <div slot="footer" class="dialog-footer">
        <el-button @click="dialogFormVisible = false">取 消</el-button>
        <el-button type="primary" @click="submitForm('visitForm')">确 定</el-button>
      </div>
    </el-dialog>
    <div class="table">
      <el-table v-loading="loading" ref="visitList" border stripe :data="visitList" style="width: 100%"
        :header-cell-style="tableHeaderStyle">
        <el-table-column align="center" prop="id" label="编号" min-width="8%"></el-table-column>
        <el-table-column align="center" prop="visitTime" label="日期" min-width="16%"></el-table-column>
        <el-table-column align="center" prop="visitText" label="回访纪要"></el-table-column>
        <el-table-column align="center" label="操作" min-width="20%">
          <template slot-scope="scope">
            <el-button @click="handleDelete(scope.row)" type="text" size="medium">删除</el-button>
          </template>
        </el-table-column>
      </el-table>
    </div>
  </div>
</template>

<script>
import * as types from "../../store/store-types";
import { deleteVisit, addVisit } from "../../api/visit";
import customerReg from "../../js/reg/customerReg";
export default {
  data() {
    return {
      loading: true,
      search: "",
      tableHeaderStyle: { background: "#a9a9a9", color: "black" },
      dialogFormVisible: false,
      visitForm: {
        visitTime: "",
        visitText: "",
      },
      formLabelWidth: "120px",
    };
  },
  created() {
    let { customerId } = this.$route.query;
    let aa = document.getElementById("box");

    if (!customerId) {
      console.log("想把客户回访页面屏蔽！");
      // document.querySelector(".filterBox").style.display = "none";
      console.log(document.querySelector(".filterBox"));

      this.loading = false;
    } else {
      this.$store.dispatch(types.VISIT_LIST, { customerId });
      this.loading = false;
    }
  },
  computed: {
    visitList() {
      return this.$store.state.visitList;
    },
  },
  methods: {
    // 删除数据
    handleDelete(row) {
      let visitId = row.id,
        customerId = row.customerId;
      deleteVisit({ visitId })
        .then((result) => {
          if (result.code == 0) {
            this.$alert("数据删除成功，即将跳转到列表~", {
              callback: () => {
                this.$store.dispatch(types.VISIT_LIST, { customerId });
              },
            });
          }
        })
        .catch(() => {
          this.$alert("数据删除失败，请重试！");
        });
    },
    submitForm() {
      let { customerId } = this.$route.query;

      let { visitTime, visitText } = this.visitForm;
      if (visitTime == "" || visitText == "") {
        this.$alert("回访日期或回访纪要不能为空！");
        return;
      }
      visitTime = this.formatTime(visitTime);
      addVisit({ customerId, visitTime, visitText })
        .then(() => {
          this.$alert("数据新增成功，即将跳转到列表~", {
            callback: () => {
              this.$store.dispatch(types.VISIT_LIST, { customerId });
              this.dialogFormVisible = false;
              this.$refs.visitForm.resetFields();
            },
          });
        })
        .catch(() => {
          this.$alert("数据新增失败，请重试！");
        });
    },
    // 日期格式化
    formatTime(time) {
      var t = new Date(time);
      let year = t.getFullYear();
      let month = this.dateIfAddZero(t.getMonth() + 1);
      let day = this.dateIfAddZero(t.getDate());

      return year + "-" + month + "-" + day;
    },
    // 日期不足十位补零
    dateIfAddZero(time) {
      return time < 10 ? "0" + time : time;
    },
  },
};
</script>

<style lang="less" scoped>
.inp {
  width: 300px;
  margin-left: 20px;
}
.box {
  height: 100%;
  overflow-y: auto;
  overflow-x: hidden;
}

/* ==filterBox== */
.filterBox {
  padding: 10px 80px;
  text-align: right;
}
</style>
