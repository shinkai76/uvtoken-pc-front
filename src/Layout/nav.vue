<!-- -->
<template>
    <div class="web_nav">
        <div class="wrap">
            <div class="nav_logo" @click="toHome">
                <div class="flext">
                    <div class="flext-all1"><img src="@/assets/img/logo_uvtoken@2x.png"/></div>
                </div>
            </div>
            <div class="nav_options m-hide">
              <ul>
                <li v-for="(link, index) in links"
                    :key="link.path"
                    @click="clickTab(link, index)"
                    :class="{'activeTab': link.path !='/whitePaper' && currentIndex == index}"
                >
                  <router-link :title="link.text" :to="link.path">
                   {{ link.text }}
                    <i class="underline"></i>
                  </router-link>
                </li>
              </ul>
            </div>
            <div class="nav_center">
<!--              隐藏注册登录-->
               <div class="in"
                     v-if="!isLogin"
                >
                   <router-link to="/signUp" style="margin-right: 12px;">
                   {{ $t("text8") }}
                   </router-link>
                   <router-link to="/login">
                   {{ $t("text9") }}
                   </router-link>
                </div>
                <div class="userInfo" v-else>
                    <el-dropdown  @command="handleClick">
                        <span class="el-dropdown-link">
                            {{userCode}}
                        </span>
                        <el-dropdown-menu slot="dropdown">
                            <el-dropdown-item command="center">
                                {{ $t("text38") }}
                            </el-dropdown-item>
                            <el-dropdown-item command="logout">
                                {{ $t("text39") }}
                            </el-dropdown-item>
                        </el-dropdown-menu>
                    </el-dropdown>
                    <i class="el-icon-arrow-down el-icon&#45;&#45;right menu-arrow"></i>
                </div>
                <div class="divider"></div>
                <!-- <div class="nav_language">
                    <el-dropdown  @command="languageChange" placement="bottom">
                        <span class="el-dropdown-link">
                           {{ currentLanguage }}
                            <i class="el-icon-arrow-down el-icon--right"></i>
                        </span>

                        <el-dropdown-menu slot="dropdown">
                            <el-dropdown-item command="zh">简体中文</el-dropdown-item>
                            <el-dropdown-item command="en">English</el-dropdown-item>
<!--                            <el-dropdown-item command="ar">عربى</el-dropdown-item>-->
<!--                            <el-dropdown-item command="ru">русский</el-dropdown-item>-->
<!--                            <el-dropdown-item command="ko">한국어</el-dropdown-item>-->
<!--                            <el-dropdown-item command="ja">日本語</el-dropdown-item>
                        </el-dropdown-menu>
                    </el-dropdown>
                </div> -->
            </div>

          <div class="m-show">
            <el-dropdown>
              <i class="el-icon-s-fold hamburg"></i>
              <el-dropdown-menu slot="dropdown">
                <el-dropdown-item v-for="link in links" :key="link.path">
                  <router-link :title="link.text" :to="link.path">
                    {{ link.text }}
                  </router-link>
                </el-dropdown-item>
              </el-dropdown-menu>
            </el-dropdown>
          </div>
        </div>


        <el-dialog  :visible.sync="loginOut" width="400px" :lock-scroll="false" :append-to-body="true" :modal-append-to-body="false" center >
            <div class="contt-text">
               {{ $t('text40') }}
            </div>
            <span slot="footer" class="dialog-btn-wrap">
                  <div role="button" class="dialog-btn confirm-button" @click="loginClose">{{ $t('text41') }}</div>
                  <div role="button" class="dialog-btn cancel-button" @click="loginOut = false">{{ $t('text42') }}</div>
            </span>
        </el-dialog>

    </div>
</template>

<script>
import {digest}  from "@/request/news"
export default {
    name: '',
    data() {
       return {
         currentLanguage: "",
         show: false,
         currentIndex: -1,
          links: [
            {
              text: this.$t("text1"),
              path: '/'
            },
            {
              text: this.$t("text2"),
              path: '/multiChain'
            },
            {
              text: this.$t("text3"),
              path: '/wallet'
            },
            {
              text: this.$t("text4"),
              path: '/financial'
            },
            {
              text: 'DApp',
              path: '/DApp'
            },
            {
              text: this.$t("text5"),
              path: '/whitePaper'
            },
            {
              text: this.$t("text6"),
              path: '/help'
            },
            {
              text: this.$t("text7"),
              path: '/presale'
            },
          ],
            map: {
                'zh': ['简体中文', 'Chinese'],
                'en': ['English', 'English'],
                'ja': ['日本語', 'Japanese'],
                'ar': ['عربى', 'Arabic'],
                'ko': ['한국어', 'Korean'],
                'ru': ['русский', 'Russian'],
            },
           pagePath:'',
           userCode:'',
           loginOut:false,
           isLogin:false,
           code:"",
           bosket:true,
           NODE_ENV:process.env.NODE_ENV,
           list:[],
           listQuery:{
               lang_type:this.$langType,
               page_no:1,
               page_size:3
           },
       }
    },
    computed:{},
    watch: {　　
        '$route': {
            handler(cur) {
              this.userCode = localStorage.getItem('filName')
              this.isLogin = localStorage.getItem('token')?true:false;
              this.pagePath = cur.path
                this.links.find((link, index)=> {
                    if (link.path == cur.path) {
                        this.currentIndex = index
                    }
                    if (cur.path == '/center')
                      this.currentIndex = -1
                })
            },
            deep: true,
            immediate: true
        }
    },
    methods:{
        toHome() {
          this.$router.push('/')
        },
        clickTab(link, index) {
          if (link.path != '/whitePaper' )
          this.currentIndex = index
        },
        get_list(){
            digest(this.listQuery).then(res=>{
                this.list = res.data.news;
                this.code = this.list[0].code;
            })
        },
        handleClick(command){
            if(command == 'center'){
                this.$router.push('/center')
            }else if(command == 'myassets'){
                this.$router.push('/myassets')

            }else if(command == 'purchase'){
                this.$router.push('/purchaseAgreement')
            }
            else{
                this.loginOut = true;
            }
        },
        loginClose(){
            this.loginOut = false
            localStorage.removeItem('token');
            localStorage.removeItem('code');
            localStorage.removeItem('email');
            localStorage.removeItem('filName');
            this.$router.push('/login')
        },

        languageChange(val){
            localStorage.setItem("lang", val)
            localStorage.setItem("langType", this.map[val][1])
            location.reload();
        },

        colseCoander(){
            this.bosket = false;
        }
    },
    created() {},
    mounted() {
        this.pagePath = this.$route.path;
        let type = localStorage.getItem('lang')
        this.currentLanguage = this.map[type][0]
    }
}

</script>

<style lang='less' scoped>
    .web_nav{
        position: relative;
        width: 100%;
        /*height:74px;*/
        /*height: 127px;*/
        z-index:100;
        background:#fff;

        .wrap{
            height:74px;
            display: flex;
            align-items: center;
            justify-content: space-between;
            color: #272727;
            width: 100%;
            padding: 0 30px;
            .nav_logo{
                font-size: 24px;
            }
            .nav_options{
                flex: 1;
                margin-left: 20px;
                ul{

                    li{
                        color: #7B839AFF;
                        opacity: 0.7;
                        font-size: 16px;
                        cursor: pointer;
                        float: left;
                        position: relative;
                        margin-right: 31px;
                        transition: all 0.5s;
                        height: 36px;
                        line-height: 36px;
                        overflow: hidden;
                        white-space: nowrap;
                        text-overflow: ellipsis;
                        margin-left: 19px;
                        a {
                          display: block;
                          height: 100%;
                        }
                        &:hover{
                          color: #5885FDFF;
                        }
                        &.activeTab{

                            color: #5885FDFF;
                        }
                    }
                }
            }
            .nav_center{
                display: flex;
                justify-content: space-between;
                align-items: center;
                font-size: 16px;
                margin-left: auto;
                .in{
                    color: #7B839AFF;
                    opacity: 0.7;
                }
                .userInfo{
                    min-width: 133px;
                    white-space: nowrap;
                    text-overflow: ellipsis;

                    .el-dropdown-link {
                      font-size: 16px;
                      font-family: PingFangSC-Regular, PingFang SC;
                      font-weight: 400;
                      color: #7B839A;
                      cursor: pointer;
                    }
                }
            }
            .divider {
                width: 1px;
                height: 36px;
                background: #F7F7F7;
                margin: 0 20px;
            }
            /deep/   .nav_language{


              /deep/ .el-dropdown{
                  font-size: 16px !important;
                  color: #7B839AFF !important;
                  opacity: 0.7;
              }
            }
            .nav_language :hover{
                cursor: pointer;
                opacity: 1;
            }
        }
    }

    .contt-text{
        font-size: 19px;
        text-align: center;
        line-height: 36px;
        color: #2C2C2C;
    }
    .flext-all1{
       float: left;
        margin-right: 8px;
    }
    .flext-all1 img{
       width: 148px;
    }
    .flext-all2{
        width: 200px;
        font-size: 24px;
        margin-top: 12px;
    }
    .raplwt{
        position: relative;

    }
    .zllet{
        cursor: pointer;
        right: 20px;
        top: 26%;
        position: absolute;
        font-size: 22px;
        color: #a6a8b5;
    }
  .dowstyle:hover{
      color:#5885FDFF ;
      cursor: pointer;
      text-decoration: underline;
  }

    .nav_options li>a i.underline {
        position: absolute;
        display: block;
        overflow: hidden;
        left: 50%;
        bottom: -5px;
        width: 0;
        height: 7px;
        background-color: #5885FD;
        opacity: 1;
        -webkit-transition: all 0.6s cubic-bezier(0.215,0.61,0.355,1) 0s;
        transition: all 0.6s cubic-bezier(0.215,0.61,0.355,1) 0s;
    }

    .nav_options li.activeTab>a i.underline, .nav_options li:hover>a i.underline {
        width: 100%;
        opacity: 1;
        left: 0;
    }
    .el-dropdown-menu {
      text-align: center;
      min-width: 140px;
      width: auto;
    }
</style>
