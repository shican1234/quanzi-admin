<template>
  <el-dialog :visible.sync="visible" :title="!dataForm.id ? $t('add') : $t('update')" :close-on-click-modal="false" :close-on-press-escape="false">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmitHandle()" :label-width="$i18n.locale === 'en-US' ? '120px' : '80px'">
          <el-form-item label="订单ID" prop="orderId">
          <el-input v-model="dataForm.orderId" placeholder="订单ID"></el-input>
      </el-form-item>
          <el-form-item label="订单号" prop="code">
          <el-input v-model="dataForm.code" placeholder="订单号"></el-input>
      </el-form-item>
          <el-form-item label="用户ID" prop="userId">
          <el-input v-model="dataForm.userId" placeholder="用户ID"></el-input>
      </el-form-item>
          <el-form-item label="下级ID" prop="subordinateId">
          <el-input v-model="dataForm.subordinateId" placeholder="下级ID"></el-input>
      </el-form-item>
          <el-form-item label="来自下级" prop="lowerLevel">
          <el-input v-model="dataForm.lowerLevel" placeholder="下级等级(下一级,下二级)"></el-input>
      </el-form-item>
          <el-form-item label="充值金额(元)" prop="totalAmount">
          <el-input v-model="dataForm.totalAmount" placeholder="总充值金额(元)"></el-input>
      </el-form-item>
      <el-form-item label="返利金额(元)" prop="obtainAmount">
          <el-input v-model="dataForm.obtainAmount" placeholder="返利金额"></el-input>
      </el-form-item>
          <el-form-item label="返利金币" prop="totalSpecies">
          <el-input v-model="dataForm.totalSpecies" placeholder="总金币"></el-input>
      </el-form-item>
          <el-form-item label="返利比例" prop="proportion">
          <el-input v-model="dataForm.proportion" placeholder="返利比例"></el-input>
      </el-form-item>
          <el-form-item label="返利前余额" prop="frontBalance">
          <el-input v-model="dataForm.frontBalance" placeholder="返利前余额"></el-input>
      </el-form-item>
          <el-form-item label="返利后余额" prop="afterBalance">
          <el-input v-model="dataForm.afterBalance" placeholder="返利后余额"></el-input>
      </el-form-item>
          <el-form-item label="返利类型(1:充值)" prop="type">
          <el-input v-model="dataForm.type" placeholder="返利类型(1:充值)"></el-input>
      </el-form-item>
          <el-form-item label="返利时间" prop="time">
          <el-input v-model="dataForm.time" placeholder="返利时间"></el-input>
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
        orderId: '',
        code: '',
        userId: '',
        subordinateId: '',
        lowerLevel: '',
        totalAmount: '',
        totalSpecies: '',
        obtainAmount: '',
        proportion: '',
        frontBalance: '',
        afterBalance: '',
        type: '',
        time: ''
      }
    }
  },
  computed: {
    dataRule () {
      return {
        orderId: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        code: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        userId: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        subordinateId: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        lowerLevel: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        totalAmount: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        totalSpecies: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        obtainAmount: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        proportion: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        frontBalance: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        afterBalance: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        type: [
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
      this.$http.get(`/InviteRebate/inviterebate/${this.dataForm.id}`).then(({ data: res }) => {
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
        this.$http[!this.dataForm.id ? 'post' : 'put']('/InviteRebate/inviterebate/', this.dataForm).then(({ data: res }) => {
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
