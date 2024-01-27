<template>
  <el-dialog :visible.sync="visible" :title="!dataForm.id ? $t('add') : $t('update')" :close-on-click-modal="false" :close-on-press-escape="false">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmitHandle()" :label-width="$i18n.locale === 'en-US' ? '120px' : '80px'">
         
      <el-form-item label="金币数量" prop="cion">
          <el-input v-model="dataForm.cion" placeholder="金币数量"></el-input>
      </el-form-item>
          <el-form-item label="提现余额" prop="balance">
          <el-input v-model="dataForm.balance" placeholder="提现余额"></el-input>
      </el-form-item>
          
        <el-form-item label="VIP到期时间" prop="vipDate">
          <el-input v-model="dataForm.vipDate" placeholder="VIP到期时间"></el-input>
      </el-form-item>
          
          <el-form-item label="上級ID" prop="pid">
          <el-input v-model="dataForm.pid" placeholder="上級ID"></el-input>
      </el-form-item>
         
          <el-form-item label="圈子内付费贴分成比例" prop="postProportion">
          <el-input v-model="dataForm.postProportion" placeholder="圈子内付费贴分成比例"></el-input>
      </el-form-item>
          <el-form-item label="提现比例" prop="withdrawalProportion">
          <el-input v-model="dataForm.withdrawalProportion" placeholder="提现比例"></el-input>
      </el-form-item>
          <el-form-item label="一级返利比例" prop="oneProportion">
          <el-input v-model="dataForm.oneProportion" placeholder="一级返利比例"></el-input>
      </el-form-item>
          <el-form-item label="二级返利比例" prop="twoProportion">
          <el-input v-model="dataForm.twoProportion" placeholder="二级返利比例"></el-input>
      </el-form-item>
      </el-form>
    <template slot="footer">
      <el-button @click="visible = false">{{ $t('cancel') }}</el-button>
      <el-button type="primary" @click="dataFormSubmitHandle()">{{ $t('confirm') }}</el-button>
    </template>
  </el-dialog>
</template>

<script>
import debounce from 'lodash/debounce'
export default {
  data () {
    return {
      visible: false,
      dataForm: {
        id: '',
        
        cion: '',
        balance: '',
       
        vipDate: '',
       
        pid: '',
        
        postProportion: '',
        withdrawalProportion: '',
        oneProportion: '',
        twoProportion: ''
      }
    }
  },
  computed: {
    dataRule () {
      return {
        cion: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        balance: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        
        vipDate: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],  
        postProportion: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        withdrawalProportion: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        oneProportion: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        twoProportion: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ]
      }
    }
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
    // 获取信息
    getInfo () {
      this.$http.get(`/user/user/${this.dataForm.id}`).then(({ data: res }) => {
        if (res.code !== 0) {
          return this.$message.error(res.msg)
        }
        this.dataForm = {
          ...this.dataForm,
          ...res.data
        }
      }).catch(() => {})
    },
    // 表单提交
    dataFormSubmitHandle: debounce(function () {
      this.$refs['dataForm'].validate((valid) => {
        if (!valid) {
          return false
        }
        this.$http[!this.dataForm.id ? 'post' : 'put']('/user/user/', this.dataForm).then(({ data: res }) => {
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
