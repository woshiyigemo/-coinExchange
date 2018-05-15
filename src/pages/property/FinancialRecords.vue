<template>
    <!-- 交易记录 -->
    <div class="financial-record" style="width: 1200px;height: 940px;">
      <div class="sjx_main">
       <div class="sjx_section">
         <div class="section_title">
            <router-link class="option" to="/property/coinoption" style="outline:none;color:#525d6f;">资产中心</router-link>
            <i class="el-icon-arrow-right"></i><span>财务记录</span>
         </div>
         <div class="sjx_btn">
           <div class="btn_section">
             <p v-for="(item,index) in arr" :key="index" @click="toggle(index)" :class="{active:index == active}">{{item}}</p>
           </div>
         </div>
         <!--充币内容-->
         <div v-if="active==0" class="charge_section" >
           <el-table class="exchange-table coinlist" :data="rechargeRecordList" style="width: 100%">
              <el-table-column prop="add_time" label="时间">
              </el-table-column>
              <el-table-column prop="currency" label="币种">
                <template slot-scope="scope">
                  {{scope.row.currency.toUpperCase()}}
                </template>
              </el-table-column>
              <el-table-column prop="number" label="数量">
              </el-table-column>
              <el-table-column prop="log_status" label="状态">
            	</el-table-column>
				    </el-table>
         </div>
         <!--提币内容-->
         <div v-if="active==1" class="charge_section">
           	<el-table class="exchange-table coinlist" :data="withdrawRecordList" style="width: 100%">
              <el-table-column prop="add_time" label="时间">
              </el-table-column>
              <el-table-column prop="currency" label="币种">
                <template slot-scope="scope">
                  {{scope.row.currency.toUpperCase()}}
                </template>
              </el-table-column>
              <el-table-column prop="number_withdraw" label="提币数量">
              </el-table-column>
              <el-table-column prop="fee" label="手续费">
            	</el-table-column>
              <el-table-column prop="number_account" label="到账数量">
            	</el-table-column>
              <el-table-column prop="log_status" label="状态">
            	</el-table-column>
				    </el-table>
         </div>
       </div>
    </div>
    </div>
</template>

<script>
import { api } from '@/static/api'
export default {
    name:'FinancialRecords',
    data(){
      return{
        active:0,
        arr:[
          "充币",
          "提币"
        ],
        withdrawRecordList:[],
        rechargeRecordList:[]
      }
    },
    methods:{
      toggle(index){
        this.active = index
      },
      getWithdrawRecordList(){
        var data = {
          page:1
        }
        api.assetsWithdrawRecord(data).then(res => {
          if(res.error_code == 1000){
            this.withdrawRecordList = res.assets_list
          }
        })
      },
      getRechargeRecordList(){
        var data = {
          page:1
        }
        api.assetsRechargeRecord(data).then(res => {
          if(res.error_code == 1000){
            this.rechargeRecordList = res.assets_list
          }
        })
      }
    },
    mounted() {
      this.getRechargeRecordList()
      this.getWithdrawRecordList()
    }
    
  }
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style lang='scss' scoped>
 li{list-style: none;}
  .sjx_main{width: 987px;}
  .sjx_section{width: 890px;margin: 0 auto;height:940px;}
  .section_title{line-height: 87px;border-bottom: 1px solid #202234;color: #525d6f;font-size: 12px;}
  .section_title span{color: #c8cdd3;}
  .sjx_btn{height:92px;border-bottom: 1px solid #202234;overflow: hidden;}
  .btn_section{width: 220px;height: 34px;margin: 30px auto;border-radius: 2px;overflow: hidden;}
  .btn_section p{width:110px;height: 34px;line-height: 34px;float: left;text-align: center;font-size:14px;color: #bcbfc8;cursor: pointer;background: #202234;}
  /*切换按钮*/
  .active{background: #4c54f9!important;color: #f6fbff!important;}
  /* 充币内容 */
  .charge_title{width:890px;height: 53px;border-bottom: 1px solid #202234;}
  .charge_title li{line-height: 53px;float: left;font-size: 12px;color: #8d9fb8;}
  .charge_title li:first-child{width:222px;;text-indent: 30px;}
  .charge_title li:nth-child(2){width: 217px;text-align: center;}
  .charge_title li:nth-child(3){width: 275px;text-align: center;}
  .charge_title li:nth-child(4){width: 174px;text-align: center;}
  .charge li{color:#ddd;font-size: 14px;}
  /* 提币内容*/
  .charge_title2 li:first-child{width: 185px;}
  .charge_title2 li:nth-child(2){width: 118px;}
  .charge_title2 li:nth-child(3){width: 154px;}
  .charge_title2 li:nth-child(4){width: 130px;}
  .charge_title2 li:nth-child(5){width: 170px;text-align: center;}
  .charge_title2 li:nth-child(6){width: 133px;text-align: center;}

</style>

