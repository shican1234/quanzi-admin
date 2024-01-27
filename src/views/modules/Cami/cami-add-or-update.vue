<template>
  <el-dialog :visible.sync="visible" :title="!dataForm.id ? $t('add') : $t('update')" :close-on-click-modal="false" :close-on-press-escape="false">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmitHandle()" :label-width="$i18n.locale === 'en-US' ? '120px' : '80px'">
          <el-form-item label="卡密价格" prop="price">
          <el-input v-model="dataForm.price" placeholder="卡密价格"></el-input>
      </el-form-item>
          <el-form-item label="卡密规格" prop="specification">
          <el-input v-model="dataForm.specification" placeholder="卡密规格(金币为个/VIP为天)"></el-input>
      </el-form-item>
          <!-- <el-form-item label="兑换类型(0:VIP  1:金币)" prop="type">
          <el-input v-model="dataForm.type" placeholder="卡密兑换类型(0:VIP  1:金币)"></el-input>
      </el-form-item> -->
      <el-form-item prop="type" label="商品类型" size="mini">
        <el-radio-group v-model="dataForm.type">
          <el-radio :label="0">VIP</el-radio>
          <el-radio :label="1">钻石</el-radio>
        </el-radio-group>
      </el-form-item>
          <!-- <el-form-item label="使用状态(0:未使用  1:已使用)" prop="status">
          <el-input v-model="dataForm.status" placeholder="使用状态(0:未使用  1:已使用)"></el-input>
      </el-form-item> -->
      <el-form-item prop="status" label="使用状态" size="mini">
        <el-radio-group v-model="dataForm.status">
          <el-radio :label="0">未使用</el-radio>
          <el-radio :label="1">已使用</el-radio>
        </el-radio-group>
      </el-form-item>
          <el-form-item label="生成数量" prop="number">
          <el-input v-model="dataForm.number" placeholder="需要生成得数量,建议在1-100间,纯数字"></el-input>
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
        camiCode: '',
        price: '',
        specification: '',
        type: '',
        status: 0,
        number: ''
      }
    }
  },
  computed: {
    dataRule () {
      return {
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
        status: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        number: [
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
      this.$http.get(`/Cami/cami/${this.dataForm.id}`).then(({ data: res }) => {
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
        this.$http[!this.dataForm.id ? 'post' : 'put']('/Cami/cami/', this.dataForm).then(({ data: res }) => {
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
