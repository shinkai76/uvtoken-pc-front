s<!-- -->
<template>
  <div class="page">
    <div class="wrap">
      <div class="page_indicator">我的资产</div>
      <div class="page_content">
        <el-table :data="tableData" style="width: 100%" stripe>
          <el-table-column prop="apply_time" label="币种">
            <template slot-scope="scope">
              <span>{{ scope.row.apply_time | trimet(that) }}</span>
            </template>
          </el-table-column>
          <el-table-column prop="round" label="可用">
            <template slot-scope="scope">
              <span>{{
                scope.row.round == 1
                  ? $t( 'text148' )
                  : $t( 'text149' )
              }}</span>
            </template>
          </el-table-column>
          <el-table-column prop="apply_amount" label="锁仓">
            <template slot-scope="scope">
              <span>{{ scope.row.apply_amount }}</span>
            </template>
          </el-table-column>
          <el-table-column prop="apply_amount" label="总资产">
            <template slot-scope="scope">
              <span>{{ scope.row.apply_amount }}</span>
            </template>
          </el-table-column>
          <el-table-column prop="apply_amount" label="操作">
            <span>充值</span>
            <span>提币</span>
          </el-table-column>
          <template slot="empty">
            <div class="noData">
              {{ $t( 'text154' ) }}
            </div>
          </template>
        </el-table>
        <!--<el-pagination @current-change="handleCurrentChange" :page-size="form.page_size"  layout="prev, pager, next" :total="total" background></el-pagination>-->
      </div>
    </div>
  </div>
</template>

<script>
import { preSaleLog } from "@/request/user.js";
export default {
  name: "",
  data() {
    return {
      that: this,
      form: {
        user_code: "",
        page_no: 1,
        page_size: 50,
      },
      tableData: [],
      total: 0
    };
  },
  computed: {},
  watch: {},
  methods: {
    handleCurrentChange(val) {
      this.form.page_no = val;
      this.getList();
    },

    getList() {
      let params = this.form;
      preSaleLog(params).then((res) => {
        if (res.code == 0) {
          this.tableData = res.data.records;
          this.total = res.data.total;
        } else {
          this.$message.error(res.msg);
        }
      });
    },
  },
  created() {
    this.form.user_code = localStorage.getItem("code");
  },
  mounted() {
    this.getList();
  },
  filters: {
    trimet(trime, that) {
      let parsTrme = that
        .$moment(Math.floor(trime * 1000))
        .format("YYYY-MM-DD HH:mm");
      return parsTrme;
    },
  },
};
</script>

<style lang='less' scoped>
.page {
  background: #f0f2f5;
  overflow: hidden;
  // height: 100%;
  box-sizing: border-box;
  .wrap {
    // height: 100%;
    .page_indicator {
      margin-top: 20px;
      height: 60px;
      line-height: 60px;
      color: #333333;
      font-size: 16px;
      padding: 0 30px;
      background: #ffffff;
    }
    .page_content {
      margin: 20px 0 100px;
      padding: 38px;
      min-height: 800px;
      background: #ffffff;
      text-align: center;
    }
  }
}
</style>


