<template>
  <el-dialog :visible.sync="visible" :title="!dataForm.id ? $t('add') : $t('update')" :close-on-click-modal="false" :close-on-press-escape="false">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmitHandle()" :label-width="$i18n.locale === 'en-US' ? '120px' : '80px'">
          <el-form-item label="显示名称" prop="duration">
          <el-input v-model="dataForm.duration" placeholder="显示名称(1个  2个   3个)"></el-input>
      </el-form-item>
          <el-form-item label="商品价格" prop="price">
          <el-input v-model="dataForm.price" placeholder="商品价格"></el-input>
      </el-form-item>
          <el-form-item label="商品规格" prop="commoditySpecification">
          <el-input v-model="dataForm.commoditySpecification" placeholder="商品规格(金币为个  会员为天或月)"></el-input>
      </el-form-item>
          <el-form-item label="商品排序" prop="commodityToSort">
          <el-input v-model="dataForm.commodityToSort" placeholder="商品排序"></el-input>
      </el-form-item>
          <!-- <el-form-item label="商品类型(1:钻石  2:VIP)" prop="type">
          <el-input v-model="dataForm.type" placeholder="商品类型(1:钻石  2:VIP)"></el-input>
      </el-form-item> -->
      <el-form-item label="商品简介" prop="briefIntroduction">
          <el-input v-model="dataForm.briefIntroduction" placeholder="商品简介(例如优惠,特惠等字数较少的介绍)"></el-input>
      </el-form-item>
      <el-form-item prop="type" label="商品类型" size="mini">
        <el-radio-group v-model="dataForm.type">
          <el-radio :label="1">钻石</el-radio>
          <el-radio :label="2">VIP</el-radio>
        </el-radio-group>
      </el-form-item>
          <!-- <el-form-item label="是否开启(1:是  2:否)" prop="status">
          <el-input v-model="dataForm.status" placeholder="是否开启(1:是  2:否)"></el-input>
      </el-form-item> -->
      <el-form-item prop="status" label="是否开启" size="mini">
        <el-radio-group v-model="dataForm.status">
          <el-radio :label="1">是</el-radio>
          <el-radio :label="2">否</el-radio>
        </el-radio-group>
      </el-form-item>
      <el-form-item prop="label" label="标签状态" size="mini">
        <el-radio-group v-model="dataForm.label">
          <el-radio :label="0">使用</el-radio>
          <el-radio :label="1">关闭</el-radio>
        </el-radio-group>
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
        duration: '',
        price: '',
        commoditySpecification: '',
        commodityToSort: '',
        type: '',
        status: '',
        label: '',
        briefIntroduction: '',
        time: ''
      }
    }
  },
  computed: {
    dataRule () {
      return {
        duration: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        price: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        commoditySpecification: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        commodityToSort: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        type: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        status: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        label: [
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
      this.$http.get(`/commodity/commodity/${this.dataForm.id}`).then(({ data: res }) => {
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
        this.$http[!this.dataForm.id ? 'post' : 'put']('/commodity/commodity/', this.dataForm).then(({ data: res }) => {
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
