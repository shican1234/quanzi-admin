<template>
  <el-dialog :visible.sync="visible" :title="!dataForm.id ? $t('add') : $t('update')" :close-on-click-modal="false" :close-on-press-escape="false">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmitHandle()" :label-width="$i18n.locale === 'en-US' ? '120px' : '80px'">
          <el-form-item label="用户ID" prop="userId">
          <el-input v-model="dataForm.userId" placeholder="用户ID"></el-input>
      </el-form-item>
          <el-form-item label="用户昵称" prop="userName">
          <el-input v-model="dataForm.userName" placeholder="充值用户昵称"></el-input>
      </el-form-item>
          <el-form-item label="商品名称" prop="commodityName">
          <el-input v-model="dataForm.commodityName" placeholder="充值商品名称"></el-input>
      </el-form-item>
          <!-- <el-form-item label="充值商品类型(1:钻石  2:VIP)" prop="commodityType">
          <el-input v-model="dataForm.commodityType" placeholder="充值商品类型(1:钻石  2:VIP)"></el-input>
      </el-form-item> -->
      <el-form-item prop="commodityType" label="商品类型" size="mini">
        <el-radio-group v-model="dataForm.commodityType" :disabled="!!dataForm.id">
          <el-radio :label="1">钻石</el-radio>
          <el-radio :label="2">VIP</el-radio>
        </el-radio-group>
      </el-form-item>
          <el-form-item label="商品规格" prop="commoditySpecification">
          <el-input v-model="dataForm.commoditySpecification" placeholder="商品规格(钻石是数量  VIP是天数)"></el-input>
      </el-form-item>
          <!-- <el-form-item label="支付类型(1:支付宝  2:微信)" prop="peyType">
          <el-input v-model="dataForm.peyType" placeholder="支付类型(1:支付宝  2:微信)"></el-input>
      </el-form-item> -->
      <el-form-item prop="payType" label="支付类型" size="mini">
        <el-radio-group v-model="dataForm.peyType" :disabled="!!dataForm.id">
          <el-radio :label="1">支付宝</el-radio>
          <el-radio :label="2">微信</el-radio>
        </el-radio-group>
      </el-form-item>
          <!-- <el-form-item label="拉起方式(1:APP  2:H5  3:小程序)" prop="peyStatus">
          <el-input v-model="dataForm.peyStatus" placeholder="拉起方式(1:APP  2:H5  3:小程序)"></el-input>
      </el-form-item> -->
      <el-form-item prop="peyStatus" label="拉起方式" size="mini">
        <el-radio-group v-model="dataForm.peyStatus" :disabled="!!dataForm.id">
          <el-radio :label="1">APP</el-radio>
          <el-radio :label="2">H5</el-radio>
          <el-radio :label="3">小程序</el-radio>
          <el-radio :label="4">公众号</el-radio>
        </el-radio-group>
      </el-form-item>
          <el-form-item label="平台订单" prop="orderCode">
          <el-input v-model="dataForm.orderCode" placeholder="平台订单号" :disabled="!!dataForm.id"></el-input>
      </el-form-item>
          <el-form-item label="三方订单" prop="trilateralCode">
          <el-input v-model="dataForm.trilateralCode" placeholder="三方支付订单号(查账订单号)" :disabled="!!dataForm.id"></el-input>
      </el-form-item>
          <el-form-item label="充值金额" prop="rechargeAmount">
          <el-input v-model="dataForm.rechargeAmount" placeholder="充值金额(元)" :disabled="!!dataForm.id"></el-input>
      </el-form-item>
          <!-- <el-form-item label="订单支付状态(0:订单创建  1:支付成功  2:支付失败  3:订单超时)" prop="orderType">
          <el-input v-model="dataForm.orderType" placeholder="订单支付状态(0:订单创建  1:支付成功  2:支付失败  3:订单超时)"></el-input>
      </el-form-item> -->
      <el-form-item prop="orderType" label="支付状态" size="mini">
        <el-radio-group v-model="dataForm.orderType" :disabled="!!dataForm.id">
          <el-radio :label="0">订单创建</el-radio>
          <el-radio :label="1">支付成功</el-radio>
          <el-radio :label="2">支付失败</el-radio>
          <el-radio :label="3">订单超时</el-radio>
        </el-radio-group>
      </el-form-item>
      </el-form>
    <template slot="footer">
<el-button @click="visible = false">{{ $t('goback') }}</el-button>
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
        userName: '',
        commodityName: '',
        commodityType: '',
        commoditySpecification: '',
        peyType: '',
        peyStatus: '',
        orderCode: '',
        trilateralCode: '',
        rechargeAmount: '',
        orderType: '',
        foundTime: ''
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
      this.$http.get(`/payOrder/payorder/${this.dataForm.id}`).then(({ data: res }) => {
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
        this.$http[!this.dataForm.id ? 'post' : 'put']('/payOrder/payorder/', this.dataForm).then(({ data: res }) => {
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
