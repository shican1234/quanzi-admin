<template>
  
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmitHandle()" label-width="120px">
      <el-form-item label="类型" size="mini">
        <el-radio-group v-model="dataForm.type">
          <el-radio :label="1">阿里云短信</el-radio>
          <el-radio :label="2">腾讯云短信</el-radio>
        </el-radio-group>
      </el-form-item>
      <template v-if="dataForm.type === 1">
        <el-form-item prop="aliyunAccessKeyId" label="accessKeyId">
          <el-input v-model="dataForm.aliyunAccessKeyId" placeholder="accessKeyId"></el-input>
        </el-form-item>
        <el-form-item prop="aliyunAccessKeySecret" label="accessKeySecret">
          <el-input v-model="dataForm.aliyunAccessKeySecret" placeholder="accessKeySecret"></el-input>
        </el-form-item>
        <el-form-item prop="aliyunSignature" label="签名">
          <el-input v-model="dataForm.aliyunSignature" placeholder="阿里云短信签名"></el-input>
        </el-form-item>
        <el-form-item prop="aliyunTemplateId" label="模版ID">
          <el-input v-model="dataForm.aliyunTemplateId" placeholder="模版ID"></el-input>
        </el-form-item>
        <el-form-item prop="aliyunTemplateName" label="模板变量参数">
          <el-input v-model="dataForm.aliyunTemplateName" placeholder="模板变量参数"></el-input>
        </el-form-item>
      </template>
      <template v-else-if="dataForm.type === 2">
        <el-form-item prop="tencentAccessKeyId" label="accessKeyId">
          <el-input v-model="dataForm.tencentAccessKeyId" placeholder="accessKeyId"></el-input>
        </el-form-item>
        <el-form-item prop="tencentAccessKeySecret" label="accessKeySecret">
          <el-input v-model="dataForm.tencentAccessKeySecret" placeholder="accessKeySecret"></el-input>
        </el-form-item>
        <el-form-item prop="tencentSignature" label="签名">
          <el-input v-model="dataForm.tencentSignature" placeholder="短信签名"></el-input>
        </el-form-item>
        <el-form-item prop="tencentTemplateId" label="模版ID">
          <el-input v-model="dataForm.tencentTemplateId" placeholder="模版ID"></el-input>
        </el-form-item>
        <el-form-item prop="tencentAppId" label="APPID">
          <el-input v-model="dataForm.tencentAppId" placeholder="APPID"></el-input>
        </el-form-item>
        <el-form-item prop="tencentAppKey" label="APPKey">
          <el-input v-model="dataForm.tencentAppKey" placeholder="APPKey"></el-input>
        </el-form-item>
      </template>
      <el-button type="primary" @click="dataFormSubmitHandle()">{{ $t('confirm') }}</el-button>
    </el-form>
</template>

<script>
import debounce from 'lodash/debounce'
export default {
  data () {
    return {
      visible: false,
      dataForm: {
        type: 1,
        aliyunAccessKeyId: '',
        aliyunAccessKeySecret: '',
        aliyunSignature: '',
        aliyunTemplateId: '',
        aliyunTemplateName: '',
        tencentAccessKeyId: '',
        tencentAccessKeySecret: '',
        tencentSignature: '',
        tencentTemplateId: '',
        tencentAppId: '',
        tencentAppKey: ''
      }
    }
  },
  computed: {
    dataRule () {
      return {
        
      }
    }
  },
  watch: {
    'dataForm.type' (val) {
      this.$refs['dataForm'].clearValidate()
    }
  },
  created(){
    this.init()
  },
  methods: {
    init () {
      this.visible = true
      this.$nextTick(() => {
        this.$refs['dataForm'].resetFields()
        this.getInfo()
      })
    },
    // 获取信息
    getInfo () {
      this.$http.get('/sys/smsconfig/info').then(({ data: res }) => {
        if (res.code !== 0) {
          return this.$message.error(res.msg)
        }
        this.dataForm = res.data
      }).catch(() => {})
    },
    // 表单提交
    dataFormSubmitHandle: debounce(function () {
      this.$refs['dataForm'].validate((valid) => {
        if (!valid) {
          return false
        }
        this.$http.post('/sys/smsconfig', this.dataForm).then(({ data: res }) => {
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
