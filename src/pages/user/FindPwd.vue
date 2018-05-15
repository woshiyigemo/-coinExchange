<template>
    <div>
        <div class="findpassword_main">

            <div class="findpassword_main_div">
              <div class="sjx_main">
                <div class="findpassword_main_div_top">
                    <div class="breadcrumb">
                        <!-- <a href="">首页 > </a>找回密码 -->
                        <el-breadcrumb separator-class="el-icon-arrow-right" v-if="isforget!=1" >
                            <el-breadcrumb-item class="sy" :to="{ path: '/account/security' }">个人中心</el-breadcrumb-item>
                            <el-breadcrumb-item class="findp">修改登录密码</el-breadcrumb-item>
                        </el-breadcrumb>
                         <el-breadcrumb separator-class="el-icon-arrow-right" v-else >
                            <el-breadcrumb-item class="sy" :to="{ path: '/user/login' }">登录页</el-breadcrumb-item>
                            <el-breadcrumb-item class="findp">找回登录密码</el-breadcrumb-item>
                        </el-breadcrumb>
                    </div>
                </div>
                <div class="step1" v-if="curStep == 1">
                    <div class="findpassword_main_div_img1"></div>
                    <div class="findpassword_main_div_email">
                        <div class="findpassword_main_div_email_left">电子邮箱</div>
                        <el-input class="findpassword_main_div_email_right" @focus="get_focus" v-model="email" placeholder="请输入邮箱地址"></el-input>
                        <!-- <div class="findpassword_main_div_email_right">

                        </div> -->
                        <transition name="fade">
                            <div class="findpassword_error" style="padding-left: 0;" v-show="isEmailErr">
                            <p class="findpassword_error_1">邮箱号错误</p>
                            <p class="findpassword_error_2">请输入注册时的邮箱</p>
                            </div>
                        </transition>

                    </div>
                    <div class="findpassword_main_div_code">
                        <div class="findpassword_main_div_code_left">验证码</div>
                        <div class="findpassword_main_div_code_right">
                            <input type="text" v-model="verifyCode"  @focus="get_focus">
                        </div>
                        <img :src="imgCodeUrl">
                        <a @click="another" style="cursor: pointer;">换一张</a>
                        <transition name="fade">
                            <div class="findpassword_error findpassword_error_3" v-show="isVerifyCodeErr">你输入的验证码错误</div>
                        </transition>
                    </div>
                    <a class="findpassword_main_div_btn" @click="toStep2"><div>下一步</div></a>
                </div>
                <div class="step2"  v-if="curStep == 2">
                    <div class="findpasswordtwo_main_div_img2"></div>
                    <div class="findpasswordtwo_main_ts">为了您的账号安全，请完成身份认证</div>
                    <div class="findpassword_main_div_email findpasswordtwo_email">
                        <div class="findpassword_main_div_email_left">电子邮箱</div>
                        <div class="findpasswordtwo_email_right">{{email}}</div>
                    </div>
                    <div class="findpassword_main_div_code findpasswordtwo_code">
                        <div class="findpassword_main_div_code_left">验证码</div>
                        <div class="findpassword_main_div_email_right">
                            <input type="text" v-model="code">
                        </div>
                        <!-- <button class="send" @click="sjx_send">发送验证码</button> -->
                        <button class="send" :disabled="counting" @click="sjx_send">
                            <countdown v-if="counting" 
                            :time="60000"
                            :leading-zero="false"
                            @countdownend="countDownEnd">
                                <template slot-scope="props">{{ props.seconds || 60 }}秒</template>
                            </countdown>
                            <span v-else>发送验证码</span>
                        </button>
                    </div>
                    <a class="findpassword_main_div_btn" @click="toStep3"><div>下一步</div></a>
                </div>
                <div class="step3"  v-if="curStep == 3">
                    <div class="findpasswordtwo_main_div_img3"></div>
                    <div class="findpasswordtwo_main_ts">您正在找回的账户是：<span>{{email}}</span></div>
                    <div class="findpassword_main_div_email findpasswordtwo_email">
                      <span class="mi">新密码</span>
                      <input class="sjx_ipt" type="password" v-model="pwd" @focus="get_focus">
                      <transition name="fade">
                        <div class="findpassword_error" style="padding-left: 0;" v-show="isEmailErr">
                            <p class="findpassword_error_3" style="background-position:10px 6px;text-indent: 30px;">长度为6~14个字符</p>
                            <p class="findpassword_error_2">支持数字,大小写字母和标点符号</p>
                            <p class="findpassword_error_1">不允许有空格</p>
                        </div>
                      </transition>
                    </div>

                     <div class="findpassword_main_div_email findpasswordtwo_email">
                       <span class="mi">确认新密码</span>
                       <input class="sjx_ipt" type="password" v-model="pwd_t" @focus="get_focus">
                       <transition name="fade">
                            <div class="findpassword_error" style="padding-left: 0;" v-show="isVerifyCodeErr">
                                <p class="findpassword_error_1">两次输入的密码不一致</p>
                            </div>
                       </transition>
                     </div>
                    <!--<a class="findpassword_main_div_btn" @click="sjx_ok">确定</a>-->
                    <a class="findpassword_main_div_btn" @click="sjx_ok"><div>下一步</div></a>
                </div>
              </div>
            </div>

        </div>
    </div>
</template>

<script>
import { api } from '@/static/api'
import { assertReturnStatement } from 'babel-types';
import countdown from '@xkeshi/vue-countdown'

export default {
    name:'FindPwd',
    props:{

    },
    data(){
        return{
            isforget:0,
            counting:false,
            curStep:1,
            email:'',
            imgCodeUrl:api.getImgCode(1001),
            verifyCode:'',
            pwd:'',
            pwd_t:'',
            isEmailErr:false,
            isVerifyCodeErr:false,
            code:''
        }
    },
    components:{
        countdown
    },
    created(){

        console.log('this.$route.params',this.$route.params.isforget)

        this.isforget = this.$route.params.isforget

        
    //    this.getImg()
    },
    mounted () {

    },
    watch:{

    },
    methods:{
        countDownEnd(){
            this.counting = false
        },
        toStep2(){
            // this.curStep = 2
            var data={
                email:this.email,
                code:this.verifyCode,
            }
            //找回密码第一步邮箱和验证码验证 2009 邮箱未注册
            api.checkLoginImgCode(data).then(res =>{
                console.log(res,111222)
                if (res.error_code == 2008 || res.error_code == 2003 || res.error_code == 2009) {
                    this.isEmailErr = true;
                }else if (res.error_code ==3010 || res.error_code == 3001){
                    this.isVerifyCodeErr = true;
                    this.imgCodeUrl = api.getImgCode(1001);
                }else{
                    this.isEmailErr = false;
                    this.isEmailErr = false;
                    this.curStep =2;
                }
            })

        },
       //邮箱验证码验证
        toStep3(){
            // this.curStep = 3
            var data={
                code:this.code
            }
            api.checkFindPwdCode(data).then(res=>{

                console.log(res,3333)
                if (res.error_code==3007) {
                  console.log("验证码不能为空")
                }else if (res.error_code == 3006){
                  console.log("验证码错误")
                }else if(res.error_code == 3005){
                  console.log("验证码过期")
                }else{
                  this.curStep = 3
                }
            })
        },
        another(){
            this.imgCodeUrl = api.getImgCode(1001)
        },
        //发送邮件
        sjx_send(){
            if(this.counting) return
				this.counting = true
            var data={
                email:this.email
            }
            api.sendFindPwdCode(data).then(res =>{
                console.log(res,"邮件已发送")
                if (res.error_code==1000) {
                    this.$message('邮件已发送')
                }
            })
        },
        //重新设置密码
        sjx_ok(){
            var data={
                password:this.pwd,
                password_t:this.pwd_t
            }
            api.setSignPwd(data).then(res =>{
                console.log(res,8888)
                if ((res.error_code!=1000)) {
                    this.isEmailErr = true
                    this.isVerifyCodeErr =true
                }else{
                  this.isEmailErr = false
                  this.isVerifyCodeErr =false
                  this.$message('密码修改成功!')
                  this.$router.push({name:'security'}) 
                } 
            })
            
        },
        get_focus(){
            this.isEmailErr =false
            this.isVerifyCodeErr =false
        }


    }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style  rel='stylesheet/scss' scoped>
.findpassword_main{width:100%;height:1020px;background:#151922;overflow:hidden}
.findpassword_main_div{background:#191f27;width:1200px;height:940px;overflow:hidden;margin:0 auto;margin-top: 40px;}
.sjx_main{width:1100px;margin: 0 auto;height: 940px;}
.findpassword_main_div_top{width:100%;height:50px;line-height:50px;border-bottom:1px solid #202234;text-align:left;font-size:14px;color:#adaeb6;padding-top:14px;}
.findpassword_main_div_top a{color:#3a4556;}
.findpassword_main_div_img1,
.findpasswordtwo_main_div_img,
.findpasswordsuccess_img{margin-left:140px;background:url('~@/assets/img/findpassword1.png') no-repeat center;width:770px;margin-top:76px;height:100px;}

.findpassword_main_div_email,.findpassword_main_div_code{width:1000px;line-height:50px;height:50px;margin-top:70px;margin-left: 50px;}
.findpassword_main_div_email_left,.findpassword_main_div_code_left{width:95px;float:left;text-align:left;color:#a2b2c8;line-height:50px;height:50px;font-size: 14px;}
.findpassword_main_div_code_right{width:300px !important;float: left;}
.send{width: 115px;height: 50px;border: 1px solid #4c54f9;color: #4c54f9;text-align: center;line-height: 47px;background: #1a232c;float: left;margin-left:24px;cursor: pointer; }
.findpasswordsuccess_main_div_code_right{width:510px;}
.findpassword_main_div_code img{float:left;margin-left:10px;height:50px;width:125px;}
.findpassword_main_div_code a{float:left;height:50px;width: 60px;float:left;margin-left:15px;color:#4c54f9;font-size:14px;}
.findpassword_main_div_btn{width:510px;height:50px;line-height:50px;color:white;display:block;background:#4c54f9;margin-top:50px;margin-left:145px;text-align: center;}
.findpassword_main_div_btn:hover div{background: rgba(255,255,255,0.1);}
.findpassword_main_div_btn:active div{background: rgba(0,0,0,0.1);}

.findpasswordtwo_main_div_img2{background:url('~@/assets/img/findpassword2.png') no-repeat center;margin-left:140px;width:770px;margin-top:76px;height:100px;}
.findpasswordtwo_main_div_img3{background:url('~@/assets/img/findpassword3.png') no-repeat center;margin-left:140px;width:770px;margin-top:76px;height:100px;}
.findpasswordtwo_main_ts{width:100%;padding-left:155px;color:#708bb0;text-align: left;margin-top: 40px;font-size:14px;}
.findpasswordtwo_email{margin-top:50px}
.findpasswordtwo_code{margin-top:30px}
.findpasswordtwo_email_right{width:360px;color:#c2c3ca;height:50px;line-height:50px;text-align:left;font-size:16px;float:left;}

.findpasswordthree_desc{width:100%;height:55px;background:url('~@/assets/img/findpassword4.png') no-repeat center;margin-top: 175px;}
.findpasswordsuccess_btn{margin-left:auto;margin-top: 90px;}
.findpasswordsuccess_img{background:url('~@/assets/img/findpassword3.png') no-repeat center;}
.findpassword_error{text-align:left;color:#8faacc;font-size:12px;padding:13px 16px;padding-left:25px;box-sizing:border-box;border-radius:2px;width:230px;position:relative;min-height:50px;height:auto;margin-left:30px;background:#3a4a5e;float: left;line-height:20px;}
.findpassword_error::after{
	content: "";
	width:0px;
    height:0px;
    border-top:6px solid rgba(0,0,0,0);
    border-right:6px solid #3a4a5e;
    border-bottom:6px solid rgba(0,0,0,0);
    border-left:6px solid rgba(0,0,0,0);
    position:absolute;
    left:0px;
    margin-left:-12px;
    top:10px;
}
.findpassword_error_1{background:#3a4a5e url('~@/assets/img/findpassword5.png') no-repeat 10px 6px;text-indent: 30px;}
.findpassword_error_2{background:#3a4a5e url('~@/assets/img/findpassword6.png') no-repeat  10px 6px;text-indent: 30px;}
.findpassword_error_3{background:#3a4a5e url('~@/assets/img/findpassword7.png') no-repeat  10px 20px;}

.mi{display: block;width: 96px;height: 50px;float: left;line-height: 50px;color: #a2b2c8;}
.sjx_ipt{width: 505px;height: 48px;border: 1px solid #384658;float: left;background: #151920;color: #a2b2c8;}

/* 提示过度动画  */
.fade-enter-active, .fade-leave-active {
  transition: opacity .5s;
}
.fade-enter, .fade-leave-to /* .fade-leave-active below version 2.1.8 */ {
  opacity: 0;
}
</style>
