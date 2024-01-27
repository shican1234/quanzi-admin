<template>
  
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmitHandle()" label-width="120px">
      <template >
        
        <el-form-item prop="smtpServer" label="服务器地址">
          <el-input v-model="dataForm.smtpServer" placeholder="smtpServer"></el-input>
        </el-form-item>
        <el-form-item prop="emailPort" label="端口">
          <el-input v-model="dataForm.emailPort" placeholder="端口"></el-input>
        </el-form-item>
        <el-form-item prop="userName" label="邮箱地址">
          <el-input v-model="dataForm.userName" placeholder="邮箱地址"></el-input>
        </el-form-item>
        <el-form-item prop="passWord" label="授权码">
          <el-input v-model="dataForm.passWord" placeholder="授权码"></el-input>
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
        smtpServer: '',
        emailPort: '',
        userName: '',
        passWord: ''
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
      this.$http.get('/sys/emailconfig/info').then(({ data: res }) => {
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
        this.$http.post('/sys/emailconfig', this.dataForm).then(({ data: res }) => {
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
