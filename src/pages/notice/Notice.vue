<template>
    <div class="notice_main">
        <div class="notice_main_header">
            <router-link class="option" to="/exchange/coinexchange" style="outline:none;color:#525d6f;">币币交易</router-link>
            <i class="el-icon-arrow-right"></i><span>公告中心</span>
        </div>
        <div v-for="(notice,index) in NoticeList" class="notice_con_div" :key='index'>
            <div class="notice_con_div_left">
                <img :src="notice.notice_img" width="100%" height="100%"/>
            </div>
            <div class="notice_con_div_right">
                <router-link class="notice_con_title" :to="{path:'/notice/detail',query:{id:notice.notice_id}}">{{notice.notice_title}}</router-link>
                <div class="notice_desc">{{notice.notice_desc}}</div>
                <div class="notice_time">
                    <div class="notice_time_left">{{notice.notice_time}}</div>
                    <div class="notice_time_right">{{notice.notice_reading}} 阅读</div>
                </div>
            </div>
        </div>
    </div>
</template>
<script>
import { api } from '@/static/api'
export default {
    name:'Notice',
    data() {
       return {
         NoticeList: []
       };
    },
    created(){
        this.GetNoticeList()
    },
    methods: {

        // 获取公告
        GetNoticeList(){
            var data = {type:1}
            api.GetNoticeList(data).then(res => {
                if(res.error_code == 1000){
                    this.NoticeList = res.data
                }else{
                    this.$message(res.error_desc)
                }
            }).catch(err => {

            })
        },
    },
}
</script>
<style>
    .notice_main{width:1200px;height:auto;min-height:200px;margin:0px auto;margin-top:40px;background:#191f27;padding:0px 50px;overflow:hidden;}
    .notice_main_header{line-height: 87px;border-bottom: 1px solid #202234;color: #525d6f;font-size: 12px;}
    .notice_main_header span{color: #c8cdd3;}
    .notice_con_div{width:1200px;height:180px;display:block;padding:20px 0px;display:flex;position:relative;}
    .notice_con_div:after{width:1100px;height:1px;content:' ';display:block;background:#202234;position:absolute;bottom:0;left:50px;}
    .notice_con_div:hover{background:#1c232c;}
    .notice_con_div_left{float:left;width:260px;height:140px;background:#2c3642;}
    .notice_con_div_right{float:left;width:800px;margin-left:40px;}
    .notice_con_title{width:100%;text-align:left;height:35px;line-height:35px;color:white;font-size:16px;}
    .notice_desc{width:100%;text-align:left;height:70px;font-size:13px;color:#8d9196;line-height:20px;}
    .notice_time{width:100%;height:35px;padding-top:10px;}
    .notice_time_left{color:#85898e;width:180px;height:20px;line-height:20px;background:url('~@/assets/img/notice1.png') no-repeat left;float:left;font-size:13px;padding-left:22px;}
    .notice_time_right{color:#85898e;width:200px;height:20px;line-height:20px;float:left;background:url('~@/assets/img/notice2.png') no-repeat left;font-size:13px;padding-left:22px;}
</style>
