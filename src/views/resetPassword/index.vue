<!-- -->
<template>
  <div class="page">
    <webNav />
    <div class="page_content">
      <div class="login_bg"></div>
      <div class="wrap">
        <div class="case forget_case">
          <div class="case_title">
            {{ $t( 'login.text10' ) }}
          </div>
          <template v-if="!resetCode">
            <el-form
              style="margin: 17px 0px 47px"
              class="case_form"
              :model="resetData"
              :rules="rules"
              ref="resetForm"
            >
              <el-form-item class="form_row" prop="mail">
                <img
                  class="input_img"
                  src="@/assets/img/login_person.png"
                  alt=""
                />
                <el-input
                  class="row_input"
                  type="text"
                  :placeholder="$t( 'login.text2' )"
                  v-model.trim="resetData.mail"
                  @keyup.enter.native="resetDataCode"
                />
              </el-form-item>
            </el-form>
            <el-button
              class="btn form_btn"
              :loading="loading"
              @click="resetDataCode"
              >{{ $t( 'login.text11' ) }}</el-button
            >
          </template>
          <template v-if="resetCode">
            <div style="margin-top: 34px">
              <div class="tstPoset">
                {{ $t('text206') }} {{ resetData.mail }}
              </div>
              <div class="tacoker">
                {{ $t('text207') }}
              </div>
            </div>
            <el-form
              class="case_form"
              :model="resetData"
              :rules="rules"
              ref="resetForm"
            >
              <el-form-item class="form_row" prop="verifyCode">
                <img
                  class="input_img"
                  src="@/assets/img/login_password.png"
                  alt=""
                />
                <el-input
                  class="row_input"
                  type="text"
                  :placeholder="$t( 'login.text5' )"
                  v-model.trim="resetData.verifyCode"
                />
                <div v-if="disabled" class="get_code">{{ btntxt }}</div>
                <div v-else class="get_code" @click="sendcode('forget')">
                  {{ btntxt }}
                </div>
              </el-form-item>
              <el-form-item class="form_row" prop="pwd">
                <img
                  class="input_img"
                  src="@/assets/img/login_password.png"
                  alt=""
                />
                <el-input
                  class="row_input"
                  autocomplete="off"
                  :type="passwordType1"
                  :placeholder="$t( 'login.text9' )"
                  v-model.trim="resetData.pwd"
                />
                <img v-show="!showEye1" @click="triggerEye(1)"
                     class="eye eye-close" src="../../assets/img/eye.svg" alt="eye">
                <img v-show="showEye1" class="eye" @click="triggerEye(1)"
                     src="../../assets/img/eye-open.svg" alt="eye">
              </el-form-item>
              <el-form-item class="form_row" prop="againPassword">
                <img
                  class="input_img"
                  src="@/assets/img/login_password.png"
                  alt=""
                />
                <el-input
                  class="row_input"
                  autocomplete="off"
                  :type="passwordType2"
                  :placeholder="$t( 'login.text7' )"
                  v-model.trim="resetData.againPassword"
                />
                <img v-show="!showEye2" @click="triggerEye(2)"
                     class="eye eye-close" src="../../assets/img/eye.svg" alt="eye">
                <img v-show="showEye2" class="eye" @click="triggerEye(2)"
                     src="../../assets/img/eye-open.svg" alt="eye">
              </el-form-item>
            </el-form>
            <el-button
              class="btn form_btn"
              style="margin-top: 20px"
              :loading="loading"
              @click="resetDataForm"
              >{{ $t( 'login.text11' ) }}</el-button
            >
          </template>
        </div>

      </div>
    </div>
    <!-- <el-dialog
      :visible.sync="verify"
      title="验证"
      center
      :close-on-click-modal="false"
      :show-close="false"
      width="400px"
    >
      <vue-simple-verify
        movedColor="#5885FDFF"
        bgColor="#fff"
        borderColor="#5885FDFF"
        ref="verify"
        @success="success"
      />
    </el-dialog> -->

    <el-dialog
          :visible.sync="dialogVisible"
          @close="closeVerifyDialog"
          :show-close="false"
          top="30vh"
          width="368px">
        <div class="verify-dialog-content">
          <div class="verify-title">{{ $t('login.text34') }}</div>
          <slideVerify :l="42"
                        :r="10"
                        :w="310"
                        :h="155"
                        :slider-text="$t('text35')"
                        @success="onSuccess"
                        @fail="onFail"
                        @refresh="onRefresh"
                        ref="slideblock"
                        :imgs="imgs"
          ></slideVerify>
        </div>
      </el-dialog>
  </div>
</template>


<script>
import webNav from "@/Layout/nav";
import {
  pubKey,
  login,
  signUp,
  mailVcode,
  passwordVcode,
  findPassword,
  resetPassword, getImg
} from "@/request/login.js";
const sha256 = require("js-sha256").sha256;
import { JSEncrypt } from "jsencrypt";
import MixinSlideVerify from "@/mixin/slideVerify.js"
let that;
import slideVerify from "@/components/slideVerify"

export default {
  name: "resetPassword",
  components: { webNav, slideVerify },
  mixins: [MixinSlideVerify],
  data() {
    var Elowert = (rule, value, callback) => {
      // let reg = /^[a-zA-Z0-9_-]+@([a-zA-Z0-9]+\.)+(com|cn|net|org)$/;
      let reg = /^(\w-*\.*)+@(\w-?)+(\.\w{2,})+$/;
      if (!reg.test(value)) {
        callback(new Error(this.$t( 'login.text13' )));
      } else {
        callback();
      }
    };
    const validatePassword = (rule, value, callback) => {
      let codeReg = /(?!^(\d+|[a-zA-Z]+|[~!@#$%^&*?]+)$)^[\w~!@#$%^&*?]{8,30}$/;
      if (!codeReg.test(value)) {
        callback(new Error(this.$t( 'login.text12' )));
      } else {
        callback();
      }
    };

    const validatePasswordAagin = (rule, value, callback) => {
      if (value != this.resetData.pwd) {
        callback(new Error(this.$t( 'login.text26' )));
      } else {
        callback();
      }
    };
    return {
      imgs: [],
      pk: '',
      showEye1: false,
      showEye2: false,
      passwordType1: 'password',
      passwordType2: 'password',
      dialogVisible: false,
      langType: this.$langType,
      fromPath: "",
      verify: false,
      loading: false,
      pagePath: "login",
      checked: false,
      disabled: false,
      signUpCode: false,
      resetCode: false,
      time: 60,
      btntxt: this.$t( 'login.text14' ),
      loginForm: {
        email: "",
        password: "",
      },
      signUpForm: {
        email: "",
        password: "",
        againPassword: "",
        verify_code: "",
        inviteCode: "",
      },
      //重置密码
      resetData: {
        mail: "",
        pwd: "",
        againPassword: "",
        verifyCode: "",
      },
      code: "",
      token: "",
      rules: {
        mail: [
          {
            required: true,
            message: this.$t( 'text214' ),
            trigger: "blur",
          },
          { validator: Elowert, required: true, trigger: "blur" },
        ],
        pwd: [
          {
            required: true,
            message: this.$t( 'text215' ),
            trigger: "blur",
          },
          { validator: validatePassword, required: true, trigger: "blur" },
        ],
        againPassword: [
          {
            required: true,
            message: this.$t( 'login.text6' ),
            trigger: "blur",
          },
          { validator: validatePassword, required: true, trigger: "blur" },
          { validator: validatePasswordAagin, required: true, trigger: "blur" },
        ],
        verifyCode: [
          {
            required: true,
            message: this.$t( 'login.text5' ),
            trigger: "blur",
          },
        ],
      },
    };
  },
  computed: {},
  watch: {
    '$route' : {
      handler(cur) {
        this.$nextTick(()=> {
          this.resetCode = false
          this.$refs.resetForm.resetFields()
        })
      }
    }
  },
  methods: {
    triggerEye(id) {
      this['showEye' + id] = !this['showEye' + id]
      if (this['passwordType' + id] == 'password') {
        this['passwordType' + id] = 'text'
      }
      else this['passwordType' + id] = 'password'
    },
    closeVerifyDialog() {
      this.loading = false
      this.handleClick()
    },
    toReset() {
      this.$router.push("forget");
    },

    rsaData(data) {
      const PUBLIC_KEY = this.pk;
      let jsencrypt = new JSEncrypt();
      jsencrypt.setPublicKey(PUBLIC_KEY);
      let result = jsencrypt.encrypt(data);
      return result;
    },

    sendcode() {
      let data = {
        lang_type: this.$langType,
        mail: this.resetData.mail,
        flag: "3",
      };
      this.timer();
      mailVcode(data).then((res) => {
        if (res.code == 0) {
          this.resetCode = true;
          this.$message({
            message: this.$t( 'login.text17' ),
            type: "success",
          });
        } else {
          if (res.code == "101702") {
            return this.$message.error(
              this.$t( 'login.text18' )
            );
          } else if (res.code == "101703") {
            return this.$message.error(
              this.$t( 'login.text19' )
            );
          } else if (res.code == "101704") {
            return this.$message.error(
              this.$t( 'login.text23' )
            );
          } else if (res.code == "101507") {
            return this.$message.error(
              this.$t( 'login.text40' )
            );
          } else {
            this.$message.error(
                this.$t( 'login.text21' )
            );
          }
        }
      });
    },

    timer() {
      if (this.time > 0) {
        this.disabled = true;
        this.time--;
        this.btntxt = " " + this.time + " s";
        setTimeout(this.timer, 1000);
      } else {
        this.time = 60;
        this.btntxt = this.$t( 'login.text14' );
        this.disabled = false;
      }
    },
    userText() {
      this.$router.push({ path: "/userAgreement" });
    },
    onSuccess() {
       setTimeout(() => {
        this.sendcode();
        this.dialogVisible = false;
      }, 500);
    },
    resetDataCode() {
      this.$refs["resetForm"].validate((valid) => {
        if (valid) {
          getImg().then(res=> {
            let url = res.data.imgUrl
            this.imgs = [url]

            // 拼图验证
            this.dialogVisible = true
          })
        }
      });
    },
    resetDataForm() {
      this.$refs["resetForm"].validate(async(valid) => {
        if (valid) {
          let params = {
            mail: this.resetData.mail,
            verifyCode: this.resetData.verifyCode,
            pwd: this.resetData.againPassword,
          };
          this.loading = true;
          let resData = await pubKey();
          if (resData.code == 0) {
            this.pk = resData.data.PubKey;
          }

          params.pwd = this.rsaData(sha256(this.resetData.pwd));
          resetPassword(params)
            .then((res) => {
              this.loading = false;
              if (res.code == 0) {
                this.$message({
                  message: this.$t( 'login.text27' ),
                  type: "success",
                });
                this.$router.push("login");
              } else {
                if (res.code == "101504") {
                  this.$message.error(
                      this.$t( 'text188' )
                  );
                }
                if (res.code == "101503") {
                  this.$message.error(
                      this.$t( 'text189' )
                  );
                }
                if (res.code == "101505") {
                  this.$message.error(
                      this.$t( 'login.text21' )
                  );
                }
                else {
                  this.$message.error(res.msg);
                }
              }
            })
            .catch(() => {});
        }
      });
    },
    loginFun() {},

    success(val) {
      setTimeout(() => {
        this.sendcode();
        this.resetCode = true;
        this.verify = false;
      }, 500);
    },
  },
  created() {
  },

  mounted() {},
};
</script>

<style lang='less' scoped>
.page {
  min-width: 1200px;
  .page_content {
    position: relative;
    /*min-height: calc(100vh - 180px);*/
    /*background-size: cover;*/
    /*background:linear-gradient(#07173C, #073863) ;*/
    .login_bg {
      position: absolute;
      position: absolute;
      top: 0;
      left: 0;
      width: 100%;
      height: 100%;
      background: url("../../assets/img/login_bg.png") 100% 100% no-repeat;
      background-size: cover;
    }
    .wrap {
      position: relative;
      z-index: 10;
      width: 100%;
      height: 100%;
      min-height: calc(100vh);
      display: flex;
      align-items: center;
      justify-content: center;
      .case {
        width: 490px;
        border-radius: 6px;
        background: #ffffff;
        padding: 40px;
        box-sizing: border-box;
        &.login_case {
          height: 490px;
        }
        .case_title {
          font-size: 30px;
          color: #262626;
          text-align: center;
        }
        .case_form {
          padding: 20px 0;
          .form_row {
            position: relative;
            /*margin-bottom: 34px;*/
            &:last-child {
              margin-bottom: 0;
            }
            .input_img {
              position: absolute;
              left: 26px;
              top: 18px;
              width: 22px;
            }
            .row_input {
              padding-left: 58px;
              font-size: 16px;
              width: 479px;
              height: 60px;
              line-height: 65px;

              border-radius: 31px;
            }
            .get_code {
              cursor: pointer;
              position: absolute;
              right: 29px;
              /* width: 60px; */
              text-align: center;
              padding: 0 4px;
              height: 40px;
              line-height: 41px;
              font-size: 16px;
              color: #909090;
              top: 14px;
              border-left: 1px solid #c7c7c7;
            }
            .get_code:hover {
              color: #5885fd;
            }
          }
          .forget_link {
            margin: 30px 0;
            text-align: right;
            font-size: 16px;
            color: #5885fd;
            cursor: pointer;
          }
        }
        &.signUp_case {
          margin: 30px 0;
          /*height: 690px;*/
          .form_row {
            margin-bottom: 26px;
          }
          .protocol_row {
            font-size: 16px;
            span {
              color: #5885fd;
            }
          }
        }

        &.forget_case {
          margin: 30px 0;
          /*height: 590px;*/
        }
        .btn {
          cursor: pointer;
          height: 50px;
          line-height: 50px;
          text-align: center;
          margin: 0 auto;
          width: 303px;
          background: #5885fd;
          border-radius: 3px;
          color: #fff;
          font-size: 20px;
          display: block;
          margin-left: 60px;
        }
      }
    }
  }
}
.page .page_content .wrap .case .singBon {
  line-height: 63px;
}
.usertext:hover {
  cursor: pointer;
  text-decoration: underline;
}
/deep/ .el-input__inner {
  width: 77%;
  font-size: 14px;
}
/deep/ .simple-verify .verify-bar {
  background-color: #5885fdff;
  top: 0px;
}
/deep/ .simple-verify .verify-bar .icon {
  width: 21px;
  height: 12px;
  box-shadow: inherit;
  border-radius: 0;
}
/deep/ .simple-verify .verify-tips {
  color: #5885fdff;
}
/deep/ .el-form-item__error {
  left: 54px;
}
.tstPoset {
  text-align: center;
  font-size: 18px;
  color: #5885fdff;
  margin-bottom: 9px;
}
.tacoker {
  font-size: 14px;
  text-align: center;
  color: #828282;
}
.eye {
  right: 36px;
  top: 22px;
}
.eye-close {
  top: 29px;
}
</style>
