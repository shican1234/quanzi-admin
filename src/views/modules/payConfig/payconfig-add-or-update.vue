<template>
  <el-dialog :visible.sync="visible" :title="!dataForm.id ? $t('add') : $t('update')" :close-on-click-modal="false" :close-on-press-escape="false">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmitHandle()" :label-width="$i18n.locale === 'en-US' ? '120px' : '80px'">
          <el-form-item label="商户名称" prop="merchantsName">
          <el-input v-model="dataForm.merchantsName" placeholder="商户名称"></el-input>
      </el-form-item>
          <el-form-item label="支付公司" prop="payName">
          <el-input v-model="dataForm.payName" placeholder="支付公司名称(例:支付宝,微信,汇付等)"></el-input>
      </el-form-item>
          <!-- <el-form-item label="支付类型(1:支付宝-官方  2:微信-官方  3:支付宝-三方  4:微信-三方)" prop="peyType">
          <el-input v-model="dataForm.peyType" placeholder="支付类型(1:支付宝-官方  2:微信-官方  3:支付宝-三方  4:微信-三方)"></el-input>
      </el-form-item> -->
      <el-form-item prop="paySource" label="所属渠道" size="mini">
        <el-radio-group v-model="dataForm.paySource">
          <el-radio :label="1">原生</el-radio>
          <el-radio :label="2">三方</el-radio>
        </el-radio-group>
      </el-form-item>
      <el-form-item prop="peyType" label="支付类型" size="mini">
        <el-radio-group v-model="dataForm.peyType">
          <el-radio :label="1">支付宝</el-radio>
          <el-radio :label="2">微信</el-radio>
        </el-radio-group>
      </el-form-item>
      <el-form-item prop="peyStatus" label="拉起方式" size="mini">
        <el-radio-group v-model="dataForm.peyStatus">
          <el-radio :label="1">APP</el-radio>
          <el-radio :label="2">H5</el-radio>
          <el-radio :label="3">小程序</el-radio>
          <el-radio :label="4">公众号</el-radio>
        </el-radio-group>
      </el-form-item>
          <!-- <el-form-item label="是否开启(1:开启  2:关闭)" prop="status">
          <el-input v-model="dataForm.status" placeholder="是否开启(1:开启  2:关闭)"></el-input>
      </el-form-item> -->
      <el-form-item prop="status" label="是否开启" size="mini">
        <el-radio-group v-model="dataForm.status">
          <el-radio :label="1">开启</el-radio>
          <el-radio :label="2">关闭</el-radio>
        </el-radio-group>
      </el-form-item>
          <el-form-item label="支付配置" prop="configurationJson">
          <el-input v-model="dataForm.configurationJson" placeholder="支付配置,如商户号,私钥,秘钥,公钥"></el-input>
      </el-form-item>
          <el-form-item label="备注" prop="remark">
          <el-input v-model="dataForm.remark" placeholder="备注(支付通道的对接注意事项以及使用说明)"></el-input>
      </el-form-item>
          <el-form-item label="显示名称" prop="showName">
          <el-input v-model="dataForm.showName" placeholder="页面显示名称(如支付宝H5,微信H5等)"></el-input>
      </el-form-item>
          <el-form-item label="调用名称" prop="callName">
          <el-input v-model="dataForm.callName" placeholder="调用名称(通道调用名称)"></el-input>
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
        merchantsName: '',
        payName: '',
        peyType: '',
        status: '',
        configurationJson: '',
        remark: '',
        showName: '',
        callName: '',
        peyStatus: '',
        paySource: '',
        time: ''
      }
    }
  },
  computed: {
    dataRule () {
      return {
        merchantsName: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        payName: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        peyType: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        status: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        configurationJson: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        remark: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        showName: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        callName: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        peyStatus: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        paySource: [
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
      this.$http.get(`/payConfig/payconfig/${this.dataForm.id}`).then(({ data: res }) => {
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
        this.$http[!this.dataForm.id ? 'post' : 'put']('/payConfig/payconfig/', this.dataForm).then(({ data: res }) => {
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
