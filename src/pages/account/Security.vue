<template>
    <div class="section">
        <div class="basic">
        <h5 class="basic_title">基本资料</h5>
        <ul class="message">
            <li>UID:</li>
            <li>{{username}}</li>
        </ul>
        <ul class="message">
            <li>等级:</li>
            <li></li>
            <li class="message_v">普通</li>
        </ul>
        <ul class="message">
            <li>登录密码:</li>
            <li>********</li>
            <li class="loginpwd" @click="changeSignPwd">修改</li>
        </ul>
        <ul class="message">
            <li>交易密码:</li>
            <li v-html="defaultPinCode"></li>
            <li v-if="hasSettedPinCode == 1" class="capitalpwd" @click="changePinCodeSet">修改</li>
            <li v-if="hasSettedPinCode == 0" class="notset"  @click="changePinCode">设置</li>
        </ul>
        </div>
        <div class="account ">
            <h5 class="basic_title">账号安全</h5>
            <!-- <ul class="message">
                <li>谷歌验证:</li>
                <li></li>
                <li class="notset">去绑定</li>
            </ul> -->
            <ul class="message">
                <li>邮箱验证:</li>
                <li>{{useremail}}</li>
                <li class="capitalpwd">修改</li>
            </ul>
        </div>

        <!-- 模态框 -->
        <el-dialog
        title="交易密码设置"
        :visible.sync="pinCodeModalVisible"
        width="394px"
        center>
            <div class="login_div_password">
                <el-tooltip class="item" effect="red" manual :value="isPwdErr" :content="err.errMsg||''" placement="right">
                    <el-input class="pwd_input" @focus="clearPwdToolTip" type="password" v-model="signPwd.pwd" placeholder="6位数字"></el-input>
                </el-tooltip>
            </div>
            <div class="login_div_password">
                <el-tooltip class="item" effect="red" manual :value="isPwdSameErr" :content="err.errMsg||''" placement="right">
                    <el-input class="pwd_input" v-model="signPwd.pwdt" @focus="clearPwdToolTip" type="password" placeholder="6位数字"></el-input>
                </el-tooltip>
            </div>
            <span slot="footer" class="dialog-footer">
                <el-button class="dialog-confirm-btn" @click="savePinCode">确 定</el-button>
            </span>
        </el-dialog>
    </div>
</template>
<script>
import { api } from '@/static/api'
import { nationalityInfo } from '@/static/dataConfig'
import { Validate } from '@/static/common'
export default {
    name:'Security',
    props:{

    },
    data(){
        return{
            pinCodeModalVisible:false,
            payPwdModalVisible:false,
            signPwd:{
                pwd:'',
                pwdt:''
            },
            isPwdErr:false,
            isPwd2Err:false,
            isPwdSameErr:false,
            err:{}
        }
    },
    computed:{
        username(){
            return this.$store.getters.username
        },
        useremail(){
            return this.$store.getters.useremail
        },
        hasSettedPinCode(){
            return this.$store.getters.hasSettedPinCode
        },
        defaultPinCode(){
            return this.$store.getters.hasSettedPincode == 1?'********':''
        }
    },
    created(){

    },
    mounted(){
        
    },
    methods:{
        changeSignPwd(){
            this.$router.replace({name:'findpassword'})
        },
        changePinCodeSet (){
            this.$router.replace({name:'findpincode'})
        },
        changePinCode(){
            this.pinCodeModalVisible = true
        },
        cancelChangePinCode(){
            this.pinCodeModalVisible = false
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
        savePinCode(){
            var self = this

            this.isPwdErr = false
            this.isPwd2Err = false
            this.isPwdSameErr = false

            self.err = Validate.pwdReset(self.signPwd.pwd,self.signPwd.pwdt)
            if(self.err.errCode == 1002){
                self.isPwdErr = true
                return
            }else if(self.err.errCode == 1003){
                self.isPwd2Err = true
                return
            }else if(self.err.errCode == 1005){
                self.isPwdSameErr = true
                return
            }

            var data = {
                password:this.signPwd.pwd,
                password_t:this.signPwd.pwdt
            }
            api.setPinCode(data)
            .then(res => {
                console.log('hahaha',res)
                if(res.error_code == 1000){
                    // this.$message(res.error_desc)
                    this.$store.dispatch('completeSettedPincode')
                    this.$message({
                        message: res.error_desc,
                        type: 'success'
                    })
                }
                this.pinCodeModalVisible = false
            }).catch(err => {
                this.pinCodeModalVisible = false
            })
        },
    }
}
</script>
<style lang="scss" scoped>

/* 右侧  */
.section{width: 987px;height: 946px;float: right;}
.basic{width: 987px;height: 437px;background: #191f27;padding: 0 50px;box-sizing: border-box;}
.basic_title{font-size: 14px;color: #c2c3ca;line-height: 54px;}
.message{height: 91px;border-top: 1px solid #202234;overflow: hidden;list-style: none;}
.message li{float: left;line-height: 91px;color: #a2b2c8;font-size: 14px;height: 91px;cursor: pointer;}
.message li:first-child{width:337px;}
.message li:nth-child(2){width:340px;text-align: right;}
.message li:nth-child(3){width:210px;text-indent: 86px;}
.message_v{background: url("~@/assets/img/v.png") 30% 50% no-repeat;}
.loginpwd{background: url("~@/assets/img/yes.png") 30% 50% no-repeat;}
.capitalpwd{background: url("~@/assets/img/yes.png") 30% 50% no-repeat;}

/* 账号安全 */
.account{width:987px;height: 495px;background: #191f27;padding: 0 50px;box-sizing: border-box;margin-top: 15px;}
.notset{background: url("~@/assets/img/wrong.png") 30% 50% no-repeat;}



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
</style>

