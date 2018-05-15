<template>
    <div class="sjx_main addressmanagement">
      <div class="sjx_section">
        <div class="sjx_sec1">
          <div class="section_title">
            <router-link class="option" to="/property/coinoption" style="outline:none;color:#525d6f;">资产中心</router-link>
            <i class="el-icon-arrow-right"></i><span>提币地址管理</span></div>
          <div class="address">
            <div class="address_left">
              <p>币种</p>
              <el-select class="coin-selecter" v-model="selectedCoin">
                <el-option class="def"
                  v-for="item in options"
                  :key="item.value"
                  :label="item.label"
                  :value="item.value">
                </el-option>
              </el-select>
            </div>
            <div class="address_right">
              <p>地址</p>
              <div class="right_sec">
                <input type="text" class="address-input" v-model="verifyInfo.newAddress"/>
                <button @click="preAddAddress">添加</button>
              </div>
            </div>
          </div>
        </div>
      </div>

      <div class="sjx_section2">
        <div class="address_list">
          <div class="address_list_title">地址列表</div>
          <div class="address_list_section">
            <ul class="clear-head">
              <li>
                <p>币种</p>
              </li>
              <li>
                <p>地址</p>
              </li>
              <li>
                <p>操作</p>
              </li>
            </ul>
            <ul class="clear" v-for="(item,index) in addressList" :key="index">
              <li>
                <p>{{item.plate_en}}</p>
              </li>
              <li >
                <p>{{item.address}}</p>
              </li>
              <li>
                <p style="color: #5e5fff;" @click="deleteAddress(item)">删除</p>
              </li>
            </ul>
          </div>
        </div>
      </div>
      <!-- 模态框 -->
      <el-dialog
        title="安全验证"
        :visible.sync="addingAddress"
        width="394px"
        center>
        <div class="login_div_password login_div_password2">
          <el-input class="pwd_input" type="text" v-model="verifyInfo.userEmail" placeholder="输入邮箱地址"></el-input>
        </div>
        <div class="login_div_password">
            <el-input class="pwd_input" type="password" v-model="verifyInfo.mailCode" placeholder="邮箱验证码" style="width:183px;"></el-input>
            <button class="pwd_btn" :disabled="counting" @click="getTakeCoinVerifyCode">
              <countdown v-if="counting" 
              :time="60000"
              :leading-zero="false"
              @countdownend="countDownEnd">
                <template slot-scope="props">{{ props.seconds || 60 }}秒</template>
              </countdown>
              <span v-else>发送验证码</span>
            </button>
        </div>
        <div class="login_div_password">
            <el-input class="pwd_input" type="password" v-model="verifyInfo.pinCode" placeholder="交易密码填写"></el-input>
        </div>
        <span slot="footer" class="dialog-footer">
                  <el-button class="dialog-confirm-btn" @click="addAddress">确 定</el-button>
              </span>
      </el-dialog>
    </div>
</template>
<script>
import { api } from '@/static/api'
import countdown from '@xkeshi/vue-countdown'
export default {
  name:'WithDrawAddressManagement',
  data(){
    return{
      counting:false,
      addingAddress:false,
      userEmail:'',
      verifyInfo:{
        newAddress:'',
        pinCode:'',
        mailCode:''
      },
      options: [{
        value: 'ETH',
        label: 'ETH'
      },{
        value: 'BTC',
        label: 'BTC'
      }],
      selectedCoin:'ETH',
      addressList:[]
    }
  },
  components:{
    countdown
  },
  created(){
    this.getAddressList()
  },
  methods:{
    countDownEnd(){
      this.counting = false
    },
    // 发送验证邮件
    getTakeCoinVerifyCode(){
      if(this.counting) return
				this.counting = true
      api.getTakeCoinVerifyCode()
      .then(res => {
        if(res.error_code == 1000){
          this.$message({
            message: '发送成功',
            type: 'success'
          })
        }
      }).catch(err => {})
    },
    // 弹出验证窗口
    preAddAddress(){
        this.addingAddress = true
        console.log(1312321313,this.selectedCoin)
    },
    // 重置弹出框
    resetPop(){
      this.addingAddress = false
      this.verifyInfo.pinCode = ''
      this.verifyInfo.mailCode = ''
      this.verifyInfo.userEmail =''
    },
    // 添加地址
    addAddress(){
      if(this.newAddress == '' || this.mailCode == ''){
            this.$message({
            message: '请先填写地址和邮箱验证码',
            type: 'error'
          })
          return
      }
      
      var data = {
        coin_name: this.selectedCoin,         // 币英文名称
        address: this.verifyInfo.newAddress,         // 提币地址
        password: this.verifyInfo.pinCode,         // 交易密码
        code:this.verifyInfo.mailCode                 //邮箱验证码
      }
      this.resetPop()
      api.addWithdrawAddress(data)
      .then(res => {
        if(res.error_code == 1000){
          this.$message({
            message: '添加成功',
            type: 'success'
          })
          this.getAddressList()
        }
      }).catch(err => {

      })
    },
    // 删除地址
    deleteAddress(item){
      var data = {
        address_id:item.address_id
      }
      api.deleteWithdrawAddress(data)
      .then(res => {
        if(res.error_code == 1000){
          this.$message({
            message: '删除成功',
            type: 'success'
          })
          this.getAddressList()
        }
      }).catch(err => {

      })
    },
    // 获取提币地址列表
    getAddressList(){
      api.withdrawAddressList()
      .then(res => {
        if(res.error_code == 1000){
          this.addressList = res.data
        }else{
          this.$message({
            message: res.error_desc,
            type: 'error'
          })
        }
      }).catch(err => {})
    }
  }
}
</script>
<style rel="stylesheet/scss" scoped>
  li{list-style: none;}
  .zuo{width: 200px;height: 940px;background: #1a232c;float: left;}
  .sjx_main{width: 987px;height: 940px;float: right;overflow: hidden;}
  .sjx_section{width: 987px;height:246px;}
  .sjx_sec1{width: 890px;margin: 0 auto;height:246px;}
  .section_title{line-height: 87px;border-bottom: 1px solid #232935;color: #525d6f;font-size: 12px;}
  .section_title span{color: #c8cdd3;}
  .address{margin-top: 17px;height: 130px;}
  .address_left,.address_right{float: left;}
  .address_left{width: 126px;}
  .address_left .el-select{width: 124px;height: 46px;border: 1px solid #444c57;line-height: 46px;background: #151922;}
  .address_left p{font-size: 12px;color: #626c78;line-height: 40px;}
  .address_right{width: 637px;margin-left: 15px;}
  .address_right p{font-size: 12px;color: #626c78;line-height: 40px;}
  .address_right input{width: 485px;height: 42px;background: #151922;border: 1px solid #444c57;color: #f5feed;}
  .address_right button{width: 100px;height: 46px;background: #585efa;border: none;color: #f5feed;margin-left:12px;cursor: pointer;}

  .sjx_section2{width:987px;height: 680px;margin-top: 15px;}
  .address_list{width: 890px;margin: 0 auto;height: 680px;}
  .address_list_title{width: 890px;height: 89px;line-height: 89px;border-bottom: 1px solid #232935;color: #525d6f;font-size: 15px;}
  .address_list_section{width: 890px;border-bottom: 1px solid #232935;}
  .clear{overflow: hidden;}
  .clear li{float: left;height: 80px;line-height: 80px;border-top:1px solid #232935;}
  .clear li:first-child{width: 100px;text-align: center;}
  .clear li:nth-child(2){width: 660px;}
  .clear li:nth-child(3){width: 130px;text-align: center;}
  .clear li p:first-child{line-height: 80px;color: #5b6777;font-size: 12px;}
  .clear li p:nth-child(2){line-height: 80px;color: #fafeff;font-size: 14px;}

  .address_list_section_head{width: 890px;border-bottom: 1px solid #232935;}
  .clear-head{overflow: hidden;}
  .clear-head li{float: left;}
  .clear-head li:first-child{width: 100px;text-align: center;}
  .clear-head li:nth-child(2){width: 660px;}
  .clear-head li:nth-child(3){width: 130px;text-align: center;}
  .clear-head li p:first-child{line-height: 40px;color: #5b6777;font-size: 12px;}
  .clear-head li p:nth-child(2){line-height: 40px;color: #fafeff;font-size: 14px;}

  .login_div_user,.login_div_password{margin-left: auto;
    margin-right: auto;margin-top:25px;width:310px;height:60px;background:url('~@/assets/img/index8.png') no-repeat left;border-bottom:1px solid #efeff0;padding-left:30px;position:relative;}
  .login_div_user input{background:none;text-align:left;font-size: 14px;color:#363a3f;line-height:60px;width:270px;height:60px;border:none;}
  .login_div_password{background:url('~@/assets/img/index9.png') no-repeat left;}
  .login_div_password2{background:url('~@/assets/img/email.png') no-repeat left;}
  .login_div_password input{background:none;text-align:left;font-size: 14px;color:#363a3f;line-height:60px;width:270px;height:60px;border:none;}
  .login_div_other{margin-left: auto;
    margin-right: auto;width:310px;height:20px;margin-top:30px;}
  .login_div_other_left{width:90px;height:20px;font-size: 12px;float:left;color:#555555;}
  .login_div_other_left a{width:12px;height:12px;border:1px solid #4e56f9;margin-top:4px;display:block;float:left;}
  .login_div_other_left_a_click{background:url('~@/assets/img/index10.png') no-repeat left;border:none !important;}
  .login_div_other_left span{height:20px;line-height:20px;float:left;margin-left:10px}
  .login_div_other_right{width:80px;height:20px;text-align:right;font-size: 12px;float:right;color:#8f94fb}
  .pwd_btn{width: 88px;height: 37px;border: 1px solid #5d5dfb;color: #5d5dfb;font-size: 12px;background: #fff;}



</style>
