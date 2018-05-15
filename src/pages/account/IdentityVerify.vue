<template>
    <div >
        <div class="real_right_title">
            <div class="title_v">实名认证</div>
            <div class="bj_ca"></div>
        </div>

        <!-- 为认证或认证被拒绝 -->
        <div class="from real-verify" v-if="authState == 1 || authState == 2">
            <el-form ref="idInfo"  :model="idInfo" label-width="120px">
                <h5>1.个人基本资料认证</h5>
                <el-form-item label="姓名：" class="base-info-line modification">

                        <el-input :placeholder="fullName" @blur="nameBlur" @focus="nameFocus" v-model="idInfo.name" clearable style="float:left;"></el-input>
                        <div class="findpassword_error" style="padding-left: 0;z-index:2000;position:absolute;top:20px;left:465px;" v-show="regname">
                            <!-- <p class="findpassword_error_3" style="background-position:10px 6px;text-indent: 30px;">长度为6~14个字符</p>
                            <p class="findpassword_error_2">支持数字,大小写字母和标点符号</p> -->
                            <p class="findpassword_error_1">{{fullName}}</p>
                        </div>
                </el-form-item>

                <el-form-item :label="labelName"  class="base-info-line modification">

                        <el-input  :placeholder="pHolder" @blur="idBlur" @focus="idFocus" v-model="idInfo.number" clearable></el-input>
                        <div class="findpassword_error" style="padding-left: 0;z-index:2000;position:absolute;top:20px;left:465px;" v-show="regId">
                            <!-- <p class="findpassword_error_3" style="background-position:10px 6px;text-indent: 30px;">长度为6~14个字符</p>
                            <p class="findpassword_error_2">支持数字,大小写字母和标点符号</p> -->
                            <p class="findpassword_error_1">{{pHolder}}</p>
                        </div>

                </el-form-item>
                <h5 style="height:100px;line-height:125px;">2.信息认证</h5>
                <div class="information">
                    <div class="port">
                        <ul>
                            <li style="text-indent: 15px;">证件正面：</li>
                            <li>
                                <img v-if="userNationality != 1" src="~@/assets/img/passport_2.png" alt="">
                                <img v-if="userNationality == 1" src="~@/assets/img/id_1.png" alt="">
                            </li>
                            <li>
                                <div class="image-preview">
                                    <img v-if="idInfo.face_image" :src="idInfo.face_image" class="avatar preview-img">
                                    <div v-else class="preview-area">
                                        <i class="el-icon-plus avatar-uploader-icon plus-mark"></i>
                                    </div>
                                </div>
                            </li>
                            <li class="upload-wrapper">
                                <el-upload
                                :action="uploadUrl"
                                :on-success="idCardUploadSuccess"
                                :show-file-list="false"
                                >
                                <el-button class="upload-btn" size="small" type="primary">上传
                                    <i class="el-icon-upload2 el-icon--right"></i>
                                </el-button>
                                </el-upload>
                                <!-- <span>上传</span>
                                <img src="~@/assets/img/chuan.png"/> -->
                            </li>
                            <li class="prompt">*上传支持jpg/png</li>
                            <li style="min-width:120px;height:120px;position: relative;" v-show="!idInfo.face_image  && submitClicked">
                                <div class="findpassword_error" style="padding-left: 0;z-index:2000;position:absolute;top:38px;left:0;min-width:156px;" >
                                    <!-- <p class="findpassword_error_3" style="background-position:10px 6px;text-indent: 30px;">长度为6~14个字符</p>
                                    <p class="findpassword_error_2">支持数字,大小写字母和标点符号</p> -->
                                    <p class="findpassword_error_1" style="min-width:120px;">{{positive}}</p>
                                </div>
                            </li>
                        </ul>
                    </div>
                    <div class="port">
                        <ul>
                            <li style="text-indent: 15px;">证件背面：</li>
                            <li>
                                <img v-if="userNationality != 1" src="~@/assets/img/passport_1.png" alt="">
                                <img v-if="userNationality == 1" src="~@/assets/img/id_2.png" alt="">
                            </li>
                            <li>
                                <div class="image-preview">
                                    <img v-if="idInfo.back_image" :src="idInfo.back_image" class="avatar preview-img">
                                    <div v-else class="preview-area">
                                        <i class="el-icon-plus avatar-uploader-icon plus-mark"></i>
                                    </div>
                                </div>
                            </li>
                            <li class="upload-wrapper">
                                <el-upload
                                :action="uploadUrl"
                                :on-success="idCardBackUploadSuccess"
                                :show-file-list="false"
                                >
                                <el-button class="upload-btn" size="small" type="primary">上传
                                    <i class="el-icon-upload2 el-icon--right "></i>
                                </el-button>
                                </el-upload>
                            </li>
                            <li class="prompt">*上传支持jpg/png</li>
                            <li style="min-width:120px;height:120px;position: relative;" v-show="!idInfo.back_image && submitClicked">
                                <div class="findpassword_error" style="padding-left: 0;z-index:2000;position:absolute;top:38px;left:0;min-width:156px;" >
                                    <!-- <p class="findpassword_error_3" style="background-position:10px 6px;text-indent: 30px;">长度为6~14个字符</p>
                                    <p class="findpassword_error_2">支持数字,大小写字母和标点符号</p> -->
                                    <p class="findpassword_error_1" style="min-width:120px;">{{back}}</p>
                                </div>
                            </li>
                        </ul>
                    </div>
                    <div  class="int port">
                        <ul>
                            <li style="text-indent: 15px;">手持证件照：</li>
                            <li>
                                <img src="~@/assets/img/id_3.png" alt="">
                            </li>
                            <li class="addimg">
                                <div class="image-preview">
                                    <img v-if="idInfo.body_image" :src="idInfo.body_image" class="avatar preview-img">
                                    <div v-else class="preview-area">
                                        <i class="el-icon-plus avatar-uploader-icon plus-mark"></i>
                                    </div>
                                </div>
                            </li>
                            <li class="upload-wrapper">
                                <el-upload
                                :action="uploadUrl"
                                :on-success="idCardBodyUploadSuccess"
                                :show-file-list="false"
                                >
                                <el-button class="upload-btn" size="small" type="primary">上传
                                    <i class="el-icon-upload2 el-icon--right"></i>
                                </el-button>
                                </el-upload>
                            </li>
                            <li class="prompt">*请提供一张手持证件照</li>
                            <li style="min-width:120px;height:120px;position: relative;" v-show="!idInfo.body_image && submitClicked">
                                <div class="findpassword_error" style="padding-left: 0;z-index:2000;position:absolute;top:38px;left:0;min-width:156px;" >
                                    <!-- <p class="findpassword_error_3" style="background-position:10px 6px;text-indent: 30px;">长度为6~14个字符</p>
                                    <p class="findpassword_error_2">支持数字,大小写字母和标点符号</p> -->
                                    <p class="findpassword_error_1" style="min-width:120px;">{{hold}}</p>
                                </div>
                            </li>
                        </ul>
                    </div>
                </div>

                <div class="subBtn"><el-button class="submit-btn" @click="saveUserVerify">提交</el-button></div>
                
            </el-form>
        </div>
        
        <!-- 完成实名认证 -->
        <div class="cpt" v-show="authState == 4">
            <div class="cpt_ok">
                <img src="~@/assets/img/complete.png" /> 
                <span>已完成实名认证~</span>
            </div>
             <el-button class="go_to_trade" @click="goExchange">去交易</el-button>
        </div>

        <!-- 实名认证审核中 -->
        <div class="cpt" v-show="authState == 3">
            <div class="cpt_ok" style="width:318px;">
                <img src="~@/assets/img/complete.png" /> 
                <span>您的资料提交成功可以去交易了~</span>
            </div>
            <!-- <div class="go_to_trade" @click="goExchange">去交易</div> -->
            <el-button class="go_to_trade" @click="goExchange">去交易</el-button>
        </div>
    </div>
</template>
<script>
import { api } from '@/static/api'
import { nationalityInfo } from '@/static/dataConfig'
import { Validate } from '@/static/common'
export default {
    name:'IdentitiVerify',
    props:{

    },
    data(){
        return{
            uploadUrl:api.uploadUrl(),
            userNationality:1,
            regname:false,
            regId:false,
            // posShow:false,
            // backShow:false,
            // holdShow:false,
            err:{},
            submitClicked:false,
            idInfo: {
                name: '',
                number:'',
                face_image:'',
                back_image:'',
                body_image:''
            }
        }
    },
    computed:{
        pHolder(){
            return this.userNationality == 1?'请输入身份证号':'请输入护照号'
        },
        labelName(){
            return this.userNationality == 1?'身份证号：':'护照号：'
        },
        fullName(){
            return this.userNationality == 1?'请输入与身份证相符的姓名':'请输入与护照相符的姓名'
        },
        positive(){
            return this.userNationality == 1?'未上传身份证正面照片':'未上传护照正面照片'
        },
        back(){
            return this.userNationality == 1?'未上传身份证背面照片':'未上传护照背面照片'
        },
        hold(){
            return this.userNationality == 1?'未上传身份证手持照片':'未上传护照手持照片'
        },
        authState(){
            return this.$store.getters.authState
        }
    },
    methods:{
        idCardUploadSuccess(response, file, fileList){
            if(response.error_code == 1000){
                this.idInfo.face_image = response.data
            }else{
                this.$message(response.error_desc)
            }
        },
        idCardBackUploadSuccess(response, file, fileList){
            if(response.error_code == 1000){
                this.idInfo.back_image = response.data
            }else{
                this.$message(response.error_desc)
            }
        },
        idCardBodyUploadSuccess(response, file, fileList){
            if(response.error_code == 1000){
                this.idInfo.body_image = response.data
            }else{
                this.$message(response.error_desc)
            }
        },
        saveUserVerify(){
            this.submitClicked =true
            var self = this
            self.err = Validate.idVerify(self.idInfo.name,self.idInfo.number,self.idInfo.face_image,self.idInfo.back_image,self.idInfo.body_image)

            if(self.err.errCode !== 1000){
                this.$message(self.err.errCode)
                return
            }
            
            var data = JSON.parse(JSON.stringify(this.idInfo))
            api.userAuth(data)
            .then(res => {
                if(res.error_code == 1000){
                    // store修改为提交中
                    this.$store.dispatch('completeAuth')
                    this.$message(res.error_desc)
                }
            }).catch(err => {

            })
        },
        goExchange(){
            this.$router.replace({name:'coinexchange'})
        },
        initUserInfo(){
            this.userNationality = this.$store.state.userInfo.nationality || 1
        },
        // 提示框的显示和隐藏
        nameBlur(){
            if(this.idInfo.name == null || this.idInfo.name.length==0){
                this.regname = true
            }
        },
        nameFocus(){
            this.regname = false
        },
        idBlur(){
            if(this.idInfo.number == null || this.idInfo.number.length==0){
                this.regId = true
            }
        },
        idFocus(){
            this.regId = false
        },

    },
    created(){
        this.initUserInfo()
    },
    mounted(){
        this.enterVerifySuccess = false
    }
}
</script>
<style lang="scss" scoped>

/* 右侧内容 */ 

.real_right_title{width: 886px;margin: 0 auto;height: 82px;margin-top: 50px;}
.title_v{float:left;width: 300px;height: 82px;font-size: 22px;color: #4148cd;background:url('~@/assets/img/v1.png') no-repeat left;background-size: contain;line-height: 82px;text-align: center;}
.bj_ca{float:right;background: url('~@/assets/img/ka.png') no-repeat;width: 153px;height: 92px;background-size: contain;}
/* 表单 */
.from{width:886px;height: 900px;margin: 0 auto;margin-top: 65px;}
.from h5{font-size: 14px;color: #c2c3ca;border-top: 1px solid #202234;line-height: 54px;}
/* 信息验证 */
.port{height: 120px;border-top: 1px solid #202234;}
.port ul{overflow: hidden;list-style: none;}
.port li{float: left;line-height: 120px;}
.port li:first-child{width: 130px;color: #a2b2c8;font-size: 14px;}
.port li img{margin: 0 auto;display: inline-block;vertical-align: middle;margin: 0 25px;}


.upload{width: 76px;height: 26px;border-radius: 30px;text-align: center;background-color: #3a4a5e;margin: 47px 34px 47px 20px;line-height: 26px!important;font-size: 12px;color: #6d86a5;cursor: pointer;}

.upload span{float: left;text-indent: 17px;}
.upload img{float: left;margin:7.5px 6px!important;}
.prompt{font-size: 12px;line-height: 120px;color: #465971;}
.int{border-bottom: 1px solid #202234;}
/* 提交 */
.submit-btn{width:310px;height: 50px;margin: 88px auto;background: #4c54f9;color:#fcfcf2;text-align: center;cursor: pointer;border: none;border-radius: 0;display: block;}

/* 去交易 */
.cpt{width:509px;margin: 0 auto; margin-top:100px;}
.cpt_ok{width: 205px;height: 48px;background-size: contain;line-height: 48px;margin: 0 auto;}
.cpt_ok img{float: left;margin-left: 8px;}
.cpt_ok span{float: left;text-indent: 25px;}
.go_to_trade{width:509px;height: 49px;background: #4c54f9;text-align: center;font-size: 18px;color: #d3d4f4;margin-top: 120px;border-radius: 0;border: none;}
.upload-wrapper{
    padding: 0 15px;
}
/* 错误提示 */
.findpassword_error{text-align:left;color:#8faacc;font-size:12px;padding:13px 16px;padding-left:25px;box-sizing:border-box;border-radius:2px;min-width:120px;position:relative;min-height:30px;height:auto;margin-left:30px;background:#3a4a5e;float: left;line-height:20px;}
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
    top:17px;
}
.findpassword_error_1{text-indent: 20px;}
// .findpassword_error_2{background:#3a4a5e url('~@/assets/img/findpassword6.png') no-repeat  10px 6px;text-indent: 30px;}
// .findpassword_error_3{background:#3a4a5e url('~@/assets/img/findpassword7.png') no-repeat  10px 20px;}

.image-preview{
        display: inline-block;
    vertical-align: middle;
    .preview-area{
        border: 1px dashed #6d86a5;
        width:80px;
        height: 54px;
        vertical-align: middle;
        display: inline-block;
        position: relative;
        margin:0 25px;
        .plus-mark{
            position: absolute;
            height: 54px;
            width: 80px;
            line-height: 54px;
            text-align: center;
            vertical-align: middle;
        }
    }
    .preview-img{
        width: 80px;
        display: block;
    }
}
.cpt_ok{ span{
    color: #fff;
}}
</style>

