<template>
  <el-dialog :visible.sync="visible" :title="!dataForm.id ? $t('add') : $t('update')" :close-on-click-modal="false" :close-on-press-escape="false">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmitHandle()" :label-width="$i18n.locale === 'en-US' ? '120px' : '80px'">
          <el-form-item label="用户ID" prop="userId">
          <el-input v-model="dataForm.userId" placeholder="用户ID"></el-input>
      </el-form-item>
          <el-form-item label="卡密号" prop="camiCode">
          <el-input v-model="dataForm.camiCode" placeholder="卡密号"></el-input>
      </el-form-item>
          <el-form-item label="卡密价格" prop="price">
          <el-input v-model="dataForm.price" placeholder="卡密价格"></el-input>
      </el-form-item>
          <el-form-item label="卡密规格(金币为个/VIP为天)" prop="specification">
          <el-input v-model="dataForm.specification" placeholder="卡密规格(金币为个/VIP为天)"></el-input>
      </el-form-item>
          <el-form-item label="卡密兑换类型(0:VIP  1:金币)" prop="type">
          <el-input v-model="dataForm.type" placeholder="卡密兑换类型(0:VIP  1:金币)"></el-input>
      </el-form-item>
          <el-form-item label="卡密表关联ID" prop="camiId">
          <el-input v-model="dataForm.camiId" placeholder="卡密表关联ID"></el-input>
      </el-form-item>
          <el-form-item label="兑换时间" prop="time">
          <el-input v-model="dataForm.time" placeholder="兑换时间"></el-input>
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
        userId: '',
        camiCode: '',
        price: '',
        specification: '',
        type: '',
        camiId: '',
        time: ''
      }
    }
  },
  computed: {
    dataRule () {
      return {
        userId: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        camiCode: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        price: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        specification: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        type: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        camiId: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        time: [
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
      this.$http.get(`/camiExchange/camiexchange/${this.dataForm.id}`).then(({ data: res }) => {
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
        this.$http[!this.dataForm.id ? 'post' : 'put']('/camiExchange/camiexchange/', this.dataForm).then(({ data: res }) => {
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
