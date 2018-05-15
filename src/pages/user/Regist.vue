<template>
    <div class="login_div">
        <div class="login_div_top">注册</div>
        <div class="login_div_nationality">
            <div class="nationality-span">
                国籍
            </div>
            <el-select v-model="nationality" placeholder="国籍">
                <el-option
                v-for="item in nationalityOption"
                :key="item.value"
                :label="item.label"
                :value="item.value">
                </el-option>
            </el-select>
        </div>
        <div class="login_div_user">
            <el-tooltip class="item" effect="red" :value="isEmailErr" manual  :content="err.errMsg||''" placement="right">
                <el-input class="username_input" v-model="email" @focus="clearEmailToolTip" placeholder="邮箱" @blur="emailrepeat"></el-input>
            </el-tooltip>
        </div>
        <div class="login_div_password">
            <el-tooltip class="item" effect="red" manual :value="isPwdErr" :content="err.errMsg||''" placement="right">
                <el-input class="pwd_input" v-model="password" @focus="clearPwdToolTip" type="password" placeholder="密码"></el-input>
            </el-tooltip>
        </div>
        <div class="login_div_password">
            <el-tooltip class="item" effect="red" manual :value="isPwd2Err||isPwdSameErr" :content="err.errMsg||''" placement="right">
                <el-input class="pwd_input" v-model="password_t" @focus="clearPwd2ToolTip" type="password" placeholder="重复密码"></el-input>
            </el-tooltip>
        </div>
        <div class="sliderbox">
            <slider @slidercomplete="getSliderStatus"></slider>
        </div>
        <div class="login_btn" @click="regist">注册</div>
    </div>
</template>

<script>

import Slider from '@/components/Slider'
import { Validate, ERR } from '@/static/common'
import { nationalityOption } from '@/static/dataConfig'
import { api } from '@/static/api'
import { functionDeclaration } from 'babel-types';

export default {
    name:'Regist',
    props:{
        
    },
    data(){
        return{
            sliderStatus:false,
            email:'',
            password:'',
            password_t:'',
            nationality:1,
            nationalityOption:nationalityOption,
            isEmailErr:false,
            isPwdErr:false,
            isPwd2Err:false,
            isNationalityErr:false,
            isPwdSameErr:false,
            err:{}
        }
    },
    components:{
        Slider:Slider
    },
    created(){
        
    },
    mounted () {
       
    },
    watch:{
        
    },
    methods:{
        regist(){
            var self = this
            
            this.isEmailErr = false
            this.isPwdErr = false
            this.isPwd2Err = false
            this.isNationalityErr = false
            this.isPwdSameErr = false

            self.err = Validate.regist(self.email,self.password,self.password_t,self.nationality,self.sliderStatus)
            if(self.err.errCode == 1001 || self.err.errCode == 2003){
                self.isEmailErr = true
                return
            }else if(self.err.errCode == 1002 || self.err.errCode == 2004){
                self.isPwdErr = true
                return
            }else if(self.err.errCode == 1003){
                self.isPwd2Err = true
                return
            }else if(self.err.errCode == 1004){
                self.isNationalityErr = true
                return
            }else if(self.err.errCode == 1005){
                self.isPwdSameErr = true
                return
            }else if(self.err.errCode == 1010){
                return
            }

            var data = {
                email:this.email,
                password:this.password,
                password_t:this.password_t,
                nationality:this.nationality
            }
            api.userRegist(data)
            .then(function(res){
                if(res.error_code == 1000){
                    self.$message({
                        message:'注册成功，即将跳转到登录页',
                        type:'sucess'
                    })
                    setTimeout(function(){
                        self.$router.push({name:'login'})
                    },3000) 
                }
            }).catch(err => {})
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
        clearPwd2ToolTip(){
            if(this.isPwd2Err || this.isPwdSameErr){
                this.isPwd2Err = false
                this.isPwdSameErr = false
            }
        },
        emailrepeat(){
            var data = {
                email:this.email,
                password:this.password,
                password_t:this.password_t,
                nationality:this.nationality
            }
            api.userRegist(data)
            .then(function(res){
                if(res.error_code == 2007){
                    this.$message(res.error_code)
                }
            }).catch(err => {})
        }
    }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style  lang='scss' scoped>

.login_div{width:400px;overflow:hidden;margin-top:135px;background:white;margin-left: auto;margin-right: auto;padding-bottom: 20px;}
.login_div_top{width:100%;height:80px;line-height:80px;color:white;font-size: 24px;background:#202832}
.login_div_nationality{
    margin-left: auto;
    margin-right: auto;
    margin-top:25px;
    width:340px;
    height:58px;
    overflow:hidden;
    position:relative;
    border:1px dashed #efeff0;
    .nationality-span{
        color: #aeaeae;
        width: 60px;
        height: 58px;
        line-height: 58px;
        display: block;
        position: absolute;
        top: 0;
        left:15px;
    }
}
.login_div_user,.login_div_password{margin-left: auto;
    margin-right: auto;margin-top:25px;width:310px;height:60px;background:url('~@/assets/img/index8.png') no-repeat left;border-bottom:1px solid #efeff0;padding-left:30px;position:relative;}
.login_div_user input{background:none;text-align:left;font-size: 14px;color:#363a3f;line-height:60px;width:270px;height:60px;border:none;} 
.login_div_password{background:url('~@/assets/img/index9.png') no-repeat left;}
.login_div_password input{background:none;text-align:left;font-size: 14px;color:#363a3f;line-height:60px;width:270px;height:60px;border:none;} 
.login_div_other{margin-left: auto;
    margin-right: auto;width:310px;height:20px;margin-top:30px;}
.login_div_other_left{width:90px;height:20px;font-size: 12px;float:left;color:#555555;}
.login_div_other_left a{width:12px;height:12px;border:1px solid #4e56f9;margin-top:4px;display:block;float:left;}
.login_div_other_left_a_click{background:url('~@/assets/img/index10.png') no-repeat left;border:none !important;}
.login_div_other_left span{height:20px;line-height:20px;float:left;margin-left:10px}
.login_div_other_right{width:80px;height:20px;text-align:right;font-size: 12px;float:right;color:#8f94fb}


.sliderbox{margin-left: auto;
    margin-right: auto;position: relative;margin-top:50px;width: 312px;height: 40px;line-height: 40px;font-size:14px;}


.login_btn{margin-left: auto;
    margin-right: auto;width:310px;height:50px;line-height:50px;color:white;font-size:18px;margin-top:50px;background:#4c54f9;}
.login_btn:hover{opacity: 0.9;}
.login_btn:active{background: #444be0;}
.login_register_div{margin-left: auto;
    margin-right: auto;width:310px;font-size: 14px;margin-top:30px;color:#8a8a8a;overflow: hidden;}
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
</style>