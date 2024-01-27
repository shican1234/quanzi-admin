<template>
  <el-dialog :visible.sync="visible" :title="!dataForm.id ? $t('add') : $t('update')" :close-on-click-modal="false" :close-on-press-escape="false">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmitHandle()" :label-width="$i18n.locale === 'en-US' ? '120px' : '80px'">
          <el-form-item label="提现单号" prop="withdrawalCode">
          <el-input v-model="dataForm.withdrawalCode" placeholder="提现单号" :disabled="!!dataForm.id"></el-input>
      </el-form-item>
          <el-form-item label="用户ID" prop="userId">
          <el-input v-model="dataForm.userId" placeholder="用户ID" :disabled="!!dataForm.id"></el-input>
      </el-form-item>

      <el-form-item label="银行名称" prop="bankName">
          <el-input v-model="dataForm.bankName" placeholder="银行名称" :disabled="!!dataForm.id"></el-input>
      </el-form-item>
      <el-form-item label="银行卡号" prop="bankNo">
          <el-input v-model="dataForm.bankNo" placeholder="银行卡号" :disabled="!!dataForm.id"></el-input>
      </el-form-item>
      <el-form-item label="持卡人" prop="name">
          <el-input v-model="dataForm.name" placeholder="持卡人" :disabled="!!dataForm.id"></el-input>
      </el-form-item>
      <el-form-item label="身份证号" prop="idno">
          <el-input v-model="dataForm.idno" placeholder="身份证号" :disabled="!!dataForm.id"></el-input>
      </el-form-item>
      <el-form-item label="提现金币" prop="withdrawalSpecies">
          <el-input v-model="dataForm.withdrawalSpecies" placeholder="提现金币" :disabled="!!dataForm.id"></el-input>
      </el-form-item>
      <el-form-item label="实际到账" prop="actualAccount">
          <el-input v-model="dataForm.actualAccount" placeholder="实际到账" :disabled="!!dataForm.id"></el-input>
      </el-form-item>
      <el-form-item label="手续费" prop="serviceCharge">
          <el-input v-model="dataForm.serviceCharge" placeholder="手续费" :disabled="!!dataForm.id"></el-input>
      </el-form-item>
      <el-form-item label="比例" prop="serviceChargeScale">
          <el-input v-model="dataForm.serviceChargeScale" placeholder="比例" :disabled="!!dataForm.id"></el-input>
      </el-form-item>
          <el-form-item label="前余额" prop="frontSpecies">
          <el-input v-model="dataForm.frontSpecies" placeholder="提现前的金豆余额" :disabled="!!dataForm.id"></el-input>
      </el-form-item>
          <el-form-item label="后余额" prop="behindSpecies">
          <el-input v-model="dataForm.behindSpecies" placeholder="提现后的金豆余额" :disabled="!!dataForm.id"></el-input>
      </el-form-item>
      <el-form-item prop="status" label="审核状态" size="mini">
        <el-radio-group v-model="dataForm.status">
          <el-radio :label="0">审核中</el-radio>
          <el-radio :label="1">审核通过</el-radio>
          <el-radio :label="2">审核拒绝</el-radio>
        </el-radio-group>
      </el-form-item>
          <el-form-item label="审核内容" prop="content">
          <el-input v-model="dataForm.content" placeholder="审核内容/代付回调内容(拒绝填写拒绝理由  成功不用填写)"></el-input>
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
        withdrawalCode: '',
        userId: '',
        withdrawalSpecies: '',
        withdrawalMoney: '',
        frontSpecies: '',
        behindSpecies: '',
        type: '',
        status: '',
        content: '',
        createTime: ''
      }
    }
  },
  computed: {
    dataRule () {
      return {
        
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
      this.$http.get(`/withdrawal/withdrawal/${this.dataForm.id}`).then(({ data: res }) => {
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
        this.$http[!this.dataForm.id ? 'post' : 'put']('/withdrawal/withdrawal/', this.dataForm).then(({ data: res }) => {
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
