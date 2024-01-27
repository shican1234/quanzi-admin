<template>
 <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmitHandle()" :label-width="$i18n.locale === 'en-US' ? '120px' : '80px'">
        <el-form-item label="APP推广" prop="appTui" >
          <el-input v-model="dataForm.appTui" placeholder="APP推广URL" class="input-item"></el-input>
      </el-form-item>
          <el-form-item label="显示公告" prop="showPopup">
          <el-radio-group v-model="dataForm.showPopup">
            <el-radio :label="0">不显示</el-radio>
            <el-radio :label="1">显示</el-radio>
          </el-radio-group>
      </el-form-item>
          <el-form-item label="公告内容" prop="popupContent">
          <el-input v-model="dataForm.popupContent" placeholder="公告显示内容" class="input-item"></el-input>
      </el-form-item>
      <el-form-item label="分享标题" prop="shareTitle">
          <el-input v-model="dataForm.shareTitle" placeholder="分享标题" class="input-item"></el-input>
      </el-form-item>
      <el-form-item label="分享LOGO" prop="appLogo">
            <el-upload
              class="avatar-uploader"
              :action="url"
              :show-file-list="false"
              :before-upload="beforeUploadHandleLogo"
              :on-success="successHandleLogo">
              <img v-if="appLogo" :src="appLogo" class="avatar">
              <i v-else class="el-icon-plus avatar-uploader-icon"></i>
            </el-upload>
      </el-form-item>
      <el-form-item label="IOS下载" prop="appIosUrl">
          <el-input v-model="dataForm.appIosUrl" placeholder="IOS下载地址" class="input-item"></el-input>
      </el-form-item>
      <el-form-item label="安卓下载" prop="appAndroidUrl">
          <el-input v-model="dataForm.appAndroidUrl" placeholder="安卓下载地址" class="input-item"></el-input>
      </el-form-item>
          <el-form-item label="开屏广告" prop="showAd">
          <el-radio-group v-model="dataForm.showAd">
            <el-radio :label="0">不显示</el-radio>
            <el-radio :label="1">显示</el-radio>
          </el-radio-group>
      </el-form-item>
          <el-form-item label="广告跳转" prop="adUrl">
          <el-input v-model="dataForm.adUrl" placeholder="开屏广告跳转的url" class="input-item"></el-input>
      </el-form-item>
          <el-form-item label="广告图片" prop="adImg">
            <el-upload
              class="avatar-uploader"
              :action="url"
              :show-file-list="false"
              :before-upload="beforeUploadHandle"
              :on-success="successHandle">
              <img v-if="adImg" :src="adImg" class="avatar">
              <i v-else class="el-icon-plus avatar-uploader-icon"></i>
            </el-upload>
      </el-form-item>
      <el-button type="primary" @click="dataFormSubmitHandle()">{{ $t('confirm') }}</el-button>
      </el-form>
      <!-- <el-button type="primary" @click="dataFormSubmitHandle()">{{ $t('confirm') }}</el-button> -->
</template>
<style>
  .avatar-uploader .el-upload {
    border: 1px dashed #d9d9d9;
    border-radius: 6px;
    cursor: pointer;
    position: relative;
    overflow: hidden;
  }
  .avatar-uploader .el-upload:hover {
    border-color: #409EFF;
  }
  .avatar-uploader-icon {
    font-size: 28px;
    color: #8c939d;
    width: 178px;
    height: 178px;
    line-height: 178px;
    text-align: center;
  }
  .avatar {
    width: 178px;
    height: 178px;
    display: block;
  }
</style>
<script>
import debounce from 'lodash/debounce'
import Cookies from 'js-cookie'
export default {
  data () {
    return {
      visible: false,
      url: '',
      adImg: '',
      appLogo: '',
      dataForm: {
        id: '',
        appTui: '',
        shareTitle: '',
        appAndroidUrl:'',
        appIosUrl:'',
        appLogo:'',
        arguments:'',
        showPopup: '',
        popupContent: '',
        showAd: '',
        adUrl: '',
        adImg: ''
      }
    }
  },
  computed: {
    dataRule () {
      return {
        
      }
    }
  },
  created(){
    this.init ()
    this.url = `${window.SITE_CONFIG['apiURL']}/sys/oss/upload?token=${Cookies.get('token')}`

  },
  methods: {
    init () {
      this.$nextTick(() => {
        this.$refs['dataForm'].resetFields()
        this.getInfo()
      })
    },
     // 上传之前
     beforeUploadHandle (file) {
       
       return true;
   },
       // 上传成功
   successHandle (res, file) {
     if(res.code == 0){
       this.adImg = res.data.src,
       this.dataForm.adImg = res.data.src
     }else{
       return this.$message.error(res.msg)
     }
   },
   // 上传之前
   beforeUploadHandleLogo (file) {
       
       return true;
   },
       // 上传成功
   successHandleLogo (res, file) {
     if(res.code == 0){
       this.appLogo = res.data.src,
       this.dataForm.appLogo = res.data.src
     }else{
       return this.$message.error(res.msg)
     }
   },
    // 获取信息
    getInfo () {
      this.$http.get(`/appconfig/appconfig/getInfo`).then(({ data: res }) => {
        if (res.code !== 0) {
          return this.$message.error(res.msg)
        }
        this.dataForm = {
          ...this.dataForm,
          ...res.data
        }
        this.appLogo = res.data.appLogo
        this.adImg = res.data.adImg
      }).catch(() => {})
    },
    // 表单提交
    dataFormSubmitHandle: debounce(function () {
      this.$refs['dataForm'].validate((valid) => {
        if (!valid) {
          return false
        }
        this.$http[!this.dataForm.id ? 'post' : 'put']('/appconfig/appconfig/', this.dataForm).then(({ data: res }) => {
          if (res.code !== 0) {
            return this.$message.error(res.msg)
          }
          this.$message({
            message: this.$t('prompt.success'),
            type: 'success',
            duration: 500,
            onClose: () => {
              this.visible = false
              this.$emit('refreshDataList')
            }
          })
        }).catch(() => {})
      })
    }, 1000, { 'leading': true, 'trailing': false })
  }
}
</script>
<style>
  .input-item{
    width: 400px;
  }
</style>