<template>
    <div class="login_div">
        <div class="login_div_top">登录</div>
        <div class="login_div_user">
            <el-tooltip class="item" effect="red" manual :value="isEmailErr" :content="err.errMsg||''" placement="right">
                <el-input class="username_input" type="text" @focus="clearEmailToolTip" v-model="email" placeholder="邮箱"></el-input>
            </el-tooltip>
        </div>
        <div class="login_div_password">
            <el-tooltip class="item" effect="red" manual :value="isPwdErr" :content="err.errMsg||''" placement="right">
                <el-input class="pwd_input" @focus="clearPwdToolTip" type="password" v-model="pwd" placeholder="密码"></el-input>
            </el-tooltip>
        </div>
        <div class="login_div_other">
            <!-- <div class="login_div_other_left"><a href="javascript:void(0);"> </a><span>记住密码</span></div> -->
            <input type="hidden" id="login_div_other_val" value="2">
            <a href="javascript:void(0);" @click="forgetPwd" class="login_div_other_right">忘记密码？</a>
        </div>
        <div class="sliderbox">
            <slider @slidercomplete="getSliderStatus"></slider>
            <p v-if="!sliderStatus && submitClicked" class="findpassword_error_1">请进行滑块认证</p>
        </div>
        
        <div class="login_btn" @click="login">登录</div>
        <div class="login_register_div">没有账号请<a href="javascript:void(0);" @click="goRegist">注册</a>?</div>
    </div>
</template>

<script>
import Slider from '@/components/Slider'
import { api } from '@/static/api'
import { Validate } from '@/static/common'
import { assertReturnStatement } from 'babel-types';

export default {
  name: 'Login',
  data () {
    return {
      submitClicked:false,
      sliderStatus:false,
      isEmailErr:false,
      isPwdErr:false,
      email:'',
      pwd:'',
      err:{}
    }
  },
  components:{
      Slider:Slider
  },
  methods:{
    login(){
        this.submitClicked = true;
        var self = this
        self.err = Validate.login(self.email,self.pwd,self.sliderStatus)
        if(self.err.errCode == 1001 || self.err.errCode == 2003){
            self.isEmailErr = true
            return
        }else if(self.err.errCode == 1002 || self.err.errCode == 2004){
            self.isPwdErr = true
            return
        }else if(self.err.errCode == 1010){
            showCodeNotSelectedError 
            return
        }
        
        var data = {
            email:self.email,
            password:self.pwd
        }

        api.userLogin(data).then(res => {            
            if(res.error_code == 1000||res.error_code == 2014){
                console.log(this.$store)
                var userInfo = {
                    name:res.data.user_name,
                    email:res.data.user_email,
                    id:res.data.user_id,
                    nationality:res.data.user_nationality,
                    hasSettedPincode:res.data.issettransactionpassword,
                    authState:res.data.authentication_status
                }
                this.$store.dispatch('userLogin',userInfo)
                this.$router.push({name:'coinexchange'})
            }
        })
    },
    getSliderStatus(status){
        this.sliderStatus = status
        console.log(this.sliderStatus,status)
    },
    clearEmailToolTip(){
        if(this.isEmailErr){
            this.isEmailErr = false
        }
    },
    clearPwdToolTip(){
        if(this.isPwdErr){
            this.isPwdErr = false
        }
    },
    goRegist(){
        this.$router.push({name:'regist'})
    },
    forgetPwd(){
        this.$router.push({name:'forgetpassword',params:{isforget:1}})
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style lang='scss' scoped>


.login_div{width:400px;height:600px;margin-top:135px;background:white;margin-left: auto;margin-right: auto;}
.login_div_top{width:100%;height:80px;line-height:80px;color:white;font-size: 24px;background:#202832}
.login_div_user,.login_div_password{
    margin-top:25px;width:310px;height:60px;background:url('~@/assets/img/index8.png') no-repeat left;border-bottom:1px solid #efeff0;padding-left:30px;position:relative;
    margin-left: auto;
    margin-right: auto;    
}
.login_div_user input{background:none;text-align:left;font-size: 14px;color:#363a3f;line-height:60px;width:270px;height:60px;border:none;} 
.login_div_password{background:url('~@/assets/img/index9.png') no-repeat left;}
.login_div_password input{background:none;text-align:left;font-size: 14px;color:#363a3f;line-height:60px;width:270px;height:60px;border:none;} 
.login_div_other{width:310px;height:20px;margin-top:30px;margin-left: auto;
    margin-right: auto;}
.login_div_other_left{width:90px;height:20px;font-size: 12px;float:left;color:#555555;}
.login_div_other_left a{width:12px;height:12px;border:1px solid #4e56f9;margin-top:4px;display:block;float:left;}
.login_div_other_left_a_click{background:url('~@/assets/img/index10.png') no-repeat left;border:none !important;}
.login_div_other_left span{height:20px;line-height:20px;float:left;margin-left:10px}
.login_div_other_right{width:80px;height:20px;text-align:right;font-size: 12px;float:right;color:#8f94fb}


.sliderbox{position: relative;margin-top:50px;width: 312px;height: 40px;line-height: 40px;font-size:14px;margin-left: auto;
    margin-right: auto;}


.login_btn{width:310px;height:50px;line-height:50px;color:white;font-size:18px;margin-top:50px;background:#4c54f9;margin-left: auto;
    margin-right: auto;}
.login_btn:hover{opacity: 0.9;}
.login_btn:active{background: #444be0;}
.login_register_div{width:310px;font-size: 14px;margin-top:30px;color:#8a8a8a;
margin-left: auto;
    margin-right: auto;
    overflow: hidden;}
.login_register_div a{color:#696ffa;}




.login_error{text-align:left;color:white;font-size:12px;padding:13px 16px;padding-left:25px;border-radius:2px;position:absolute;min-height:30px;height:auto;margin-left:335px;background:#fc3759;float: left;line-height:20px;top:10px;width:120px;}
.login_error::after{
	content: "";
	width:0px;
    height:0px;
    border-top:6px solid rgba(0,0,0,0);
    border-right:6px solid #fc3759;
    border-bottom:6px solid rgba(0,0,0,0);
    border-left:6px solid rgba(0,0,0,0);
    position:absolute;
    left:0px;
    margin-left:-12px;
    top:10px;
}
.login_error_1{background:#fc3759 url('~@/assets/img/findpassword8.png') no-repeat  10px 20px;}

//错误提示
.findpassword_error_1{text-align:center;color: #fc3759;font-size: 12px;}

</style>
