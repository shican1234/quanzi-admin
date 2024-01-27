<template>
  <el-dialog :visible.sync="visible" :title="!dataForm.id ? $t('add') : $t('update')" :close-on-click-modal="false" :close-on-press-escape="false">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmitHandle()" :label-width="$i18n.locale === 'en-US' ? '120px' : '80px'">
          <!-- <el-form-item label="客服二维码url链接" prop="customerQrCodeUrl">
          <el-input v-model="dataForm.customerQrCodeUrl" placeholder="客服二维码url链接"></el-input>
      </el-form-item> -->
      <el-form-item label="客服二维码" prop="customerQrCodeUrl">
            <el-upload
              class="avatar-uploader"
              :action="url"
              :show-file-list="false"
              :before-upload="customerQrCodeUpload"
              :on-success="successcustomerQrCodeUpload">
              <img v-if="customerQrCodeUrl" :src="customerQrCodeUrl" class="avatar">
              <i v-else class="el-icon-plus avatar-uploader-icon"></i>
            </el-upload>
      </el-form-item>
          <el-form-item label="客服微信号" prop="customerWechat">
          <el-input v-model="dataForm.customerWechat" placeholder="客服微信号"></el-input>
      </el-form-item>
          <el-form-item label="公众号名称" prop="accountName">
          <el-input v-model="dataForm.accountName" placeholder="公众号名称"></el-input>
      </el-form-item>
      <el-form-item label="联系电话" prop="telePhone">
          <el-input v-model="dataForm.telePhone" placeholder="联系电话"></el-input>
      </el-form-item>
      </el-form>
    <template slot="footer">
      <el-button @click="visible = false">{{ $t('cancel') }}</el-button>
      <el-button type="primary" @click="dataFormSubmitHandle()">{{ $t('confirm') }}</el-button>
    </template>
  </el-dialog>
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
      customerQrCodeUrl: '',
      dataForm: {
        id: '',
        customerQrCodeUrl: '',
        customerWechat: '',
        accountName: ''
      }
    }
  },
  computed: {
    dataRule () {
      return {
        customerQrCodeUrl: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ]
      }
    }
  },
  created(){
    this.init ()
    this.url = `${window.SITE_CONFIG['apiURL']}/sys/oss/upload?token=${Cookies.get('token')}`

  },
  methods: {
    init () {
      this.visible = true
      this.$nextTick(() => {
        this.$refs['dataForm'].resetFields()
        if (this.dataForm.id) {
          this.getInfo()
        }
      })
    },
     // 上传之前
     customerQrCodeUpload (file) {
       
       return true;
   },
   successcustomerQrCodeUpload (res, file) {
     if(res.code == 0){
       this.customerQrCodeUrl = res.data.src,
       this.dataForm.customerQrCodeUrl = res.data.src
     }else{
       return this.$message.error(res.msg)
     }
   },
    // 获取信息
    getInfo () {
      this.$http.get(`/customer/customer/${this.dataForm.id}`).then(({ data: res }) => {
        if (res.code !== 0) {
          return this.$message.error(res.msg)
        }
        this.dataForm = {
          ...this.dataForm,
          ...res.data
        }
        this.customerQrCodeUrl= this.dataForm.customerQrCodeUrl
      }).catch(() => {})
    },
    // 表单提交
    dataFormSubmitHandle: debounce(function () {
      this.$refs['dataForm'].validate((valid) => {
        if (!valid) {
          return false
        }
        this.$http[!this.dataForm.id ? 'post' : 'put']('/customer/customer/', this.dataForm).then(({ data: res }) => {
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
