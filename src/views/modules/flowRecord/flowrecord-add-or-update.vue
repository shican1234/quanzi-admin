<template>
  <el-dialog :visible.sync="visible" :title="!dataForm.id ? $t('add') : $t('update')" :close-on-click-modal="false" :close-on-press-escape="false">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmitHandle()" :label-width="$i18n.locale === 'en-US' ? '120px' : '80px'">
          <el-form-item label="用户ID" prop="userId">
          <el-input v-model="dataForm.userId" placeholder="用户ID"></el-input>
      </el-form-item>
          <el-form-item label="金币数量" prop="amount">
          <el-input v-model="dataForm.amount" placeholder="金币数量"></el-input>
      </el-form-item>
          <el-form-item label="前数量" prop="frontBalance">
          <el-input v-model="dataForm.frontBalance" placeholder="前金币数量"></el-input>
      </el-form-item>
          <el-form-item label="后数量" prop="afterBalance">
          <el-input v-model="dataForm.afterBalance" placeholder="后金币数量"></el-input>
      </el-form-item>
          <el-form-item label="说明" prop="illuStrate">
          <el-input v-model="dataForm.illuStrate" placeholder="说明"></el-input>
      </el-form-item>
          <el-form-item label="来源" prop="amountSource">
          <el-input v-model="dataForm.amountSource" placeholder="来源(返利,消耗)"></el-input>
      </el-form-item>
          <el-form-item label="时间" prop="time">
          <el-input v-model="dataForm.time" placeholder="时间"></el-input>
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
        amount: '',
        frontBalance: '',
        afterBalance: '',
        illuStrate: '',
        amountSource: '',
        time: ''
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
      this.$http.get(`/flowRecord/flowrecord/${this.dataForm.id}`).then(({ data: res }) => {
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
        this.$http[!this.dataForm.id ? 'post' : 'put']('/flowRecord/flowrecord/', this.dataForm).then(({ data: res }) => {
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
