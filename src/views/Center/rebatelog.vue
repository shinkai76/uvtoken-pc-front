
<!-- -->
<template>
  <div class="page">
    <div class="wrap">
      <div class="page_indicator">
        <router-link class="gobaket" dir="ltr" to="/center">{{
          $t( 'text114' )
        }}</router-link>
        > {{ $t( 'text159' ) }}
      </div>
      <div class="page_content">
        <el-table :data="tableData" style="width: 100%" stripe>
          <el-table-column
            prop="rebate_time"
            width="180"
            :label="$t( 'text146' )"
          >
            <template slot-scope="scope">
              <span v-if="scope.row.rebate_time">{{
                scope.row.rebate_time | trimet(that)
              }}</span>
              <span v-else></span>
            </template>
          </el-table-column>
          <el-table-column
            prop="Username"
            :label="$t( 'text156' )"
          ></el-table-column>

          <el-table-column
            prop="round "
            :label="$t( 'text160' )"
          >
            <template slot-scope="scope">
              <span>{{
                scope.row.RebateLevel == 1
                  ? $t( 'text161' )
                  : $t( 'text162' )
              }}</span>
            </template>
          </el-table-column>

          <el-table-column
            prop="round "
            :label="$t( 'text147' )"
          >
            <template slot-scope="scope">
              <span>
                {{
                  scope.row.Round == 1
                    ? $t( 'text150' )
                    : scope.row.Round == 2
                    ? $t( 'text151' )
                    : $t( 'text152' )
                }}
              </span>
            </template>
          </el-table-column>
          <el-table-column
            prop="ApplyAmount"
            :width="['en','ru'].includes($lang)? 280: 200"
            :label="$t( 'text153' )  + '    (USDT)'"
          ></el-table-column>
          <el-table-column
            prop="RebateAmount"
            :width="['en','ru'].includes($lang)? 280: 200"
            :label="$t( 'text163' ) + '    (USDT)'"
          ></el-table-column>

          <template slot="empty">
            <div class="noData">
              {{ $t( 'text154' ) }}
            </div>
          </template>
        </el-table>
        <el-pagination
          @current-change="handleCurrentChange"
          :page-size="form.page_size"
          layout="prev, pager, next"
          :total="total"
          background
        ></el-pagination>
      </div>
    </div>
  </div>
</template>

<script>
import { rebateLog } from "@/request/user.js";
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
      total: 0,
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
      rebateLog(params).then((res) => {
        if (res.code == 0) {
          this.tableData = res.data.records;
          this.total = res.count;
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


