<template>
  <el-dialog :visible.sync="visible" :title="!dataForm.id ? $t('add') : $t('update')" :close-on-click-modal="false" :close-on-press-escape="false">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmitHandle()" :label-width="$i18n.locale === 'en-US' ? '120px' : '80px'">
          <el-form-item label="版本号" prop="version">
          <el-input v-model="dataForm.version" placeholder="版本号"></el-input>
      </el-form-item>
          <el-form-item label="更新内容" prop="note">
          <el-input v-model="dataForm.note" placeholder="更新内容"></el-input>
      </el-form-item>
          <el-form-item label="更新地址" prop="downloadUrl">
          <el-input v-model="dataForm.downloadUrl" placeholder="更新地址"></el-input>
      </el-form-item>
          <el-form-item label="平台" prop="os">
           
              <el-select v-model="dataForm.os" filterable placeholder="请选择">
                <el-option key="android" label="android" value="android" >
                </el-option>
                <el-option key="ios" label="ios" value="ios" >
                </el-option>
             </el-select>
      </el-form-item>
      <el-form-item label="是否强制更新" prop="forces">
           
           <el-select v-model="dataForm.forces" filterable placeholder="请选择">
             <el-option :key="0" label="不强制" :value="0" >
             </el-option>
             <el-option :key="1" label="强制" :value="1" >
             </el-option>
          </el-select>
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
        version: '',
        note: '',
        forces:'',
        downloadUrl: '',
        os: ''
      }
    }
  },
  computed: {
    dataRule () {
      return {
        version: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        note: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        downloadUrl: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        os: [
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
      this.$http.get(`/appupdate/appupdate/${this.dataForm.id}`).then(({ data: res }) => {
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
        this.$http[!this.dataForm.id ? 'post' : 'put']('/appupdate/appupdate/', this.dataForm).then(({ data: res }) => {
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
