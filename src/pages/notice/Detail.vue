<template>
    <div class="notice_main">
        <div class="notice_main_header">
            <router-link class="option" to="/exchange/coinexchange" style="outline:none;color:#525d6f;">币币交易</router-link>
            <i class="el-icon-arrow-right"></i><router-link class="option" to="/notice" style="outline:none;color:#525d6f;">公告中心</router-link>
            <i class="el-icon-arrow-right"></i><span>公告详情</span>
        </div>
        <div class="notice_main_title">{{NoticeDetail.notice_title}}</div>
        <div class="notice_main_desc">{{NoticeDetail.notice_desc}}</div>
        <div class="notice_detail_time">
            <div class="notice_detail_time_left">{{NoticeDetail.notice_time}}</div>
            <div class="notice_detail_time_right">{{NoticeDetail.notice_reading}}</div>
        </div>
        <div class="notice_con" v-html="NoticeDetail.notice_content"></div>
    </div>
</template>
<script>
import { api } from '@/static/api'
export default {
    name:'Notice',
    data() {
       return {
         NoticeDetail: {},
         id: '',
       };
    },
    created(){
        this.GetNoticeDetail(this.$route.query.id),
        this.SetNoticeReadingCount(this.$route.query.id)
    },
    methods: {

        // 获取公告
        GetNoticeDetail(id){
            var data = {id:id}
            api.GetNoticeDetail(data).then(res => {
                if(res.error_code == 1000){
                    this.NoticeDetail = res.data
                }else{
                    this.$message(res.error_desc)
                }
            }).catch(err => {

            })
        },
        //添加阅读量
        SetNoticeReadingCount(id){
            var data = {id:id}
            api.SetNoticeReadingCount(data).then(res => {
                if(res.error_code == 1000){
                }else{
                    this.$message(res.error_desc)
                }
            }).catch(err => {
            })
        }
    },
}
</script>
<style>
    .notice_main{width:1200px;height:auto;min-height:200px;margin:0px auto;margin-top:40px;background:#191f27;overflow:hidden;}
    .notice_main_header{line-height: 87px;border-bottom: 1px solid #202234;color: #525d6f;font-size: 12px;}
    .notice_main_header span{color: #c8cdd3;}
    .notice_main_title{padding:0px 50px;width:100%;margin-top:20px;height:24px;line-height:24px;font-size:20px;color:white;text-align:left;}
    .notice_main_desc{padding:0px 50px;width:100%;height:70px;margin-top:15px;line-height:22px;font-size:14px;color:#767a7f;}
    .notice_detail_time{padding:0px 50px;width:100%;height:45px;padding-top:10px;}
    .notice_detail_time_left{color:#85898e;width:180px;height:20px;line-height:20px;background:url('~@/assets/img/notice1.png') no-repeat left;float:left;font-size:13px;padding-left:22px;}
    .notice_detail_time_right{color:#85898e;width:200px;height:20px;line-height:20px;float:left;background:url('~@/assets/img/notice2.png') no-repeat left;font-size:13px;padding-left:22px;}

    .notice_con{padding:40px 50px;background:#1c232c;width:100%;height:auto;min-height:150px;line-height:22px;font-size:14px;color:white;}
</style>
