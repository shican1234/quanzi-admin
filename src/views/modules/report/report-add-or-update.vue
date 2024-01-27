<template>
  <el-dialog :visible.sync="visible" :title="!dataForm.id ? $t('add') : $t('update')" :close-on-click-modal="false" :close-on-press-escape="false">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmitHandle()" :label-width="$i18n.locale === 'en-US' ? '120px' : '80px'">
          <el-form-item label="处理进度(0待处理1已处理2已关闭)" prop="status">
          <el-input v-model="dataForm.status" placeholder="处理进度(0待处理1已处理2已关闭)"></el-input>
      </el-form-item>
          <el-form-item label="举报者Id" prop="fromUser">
          <el-input v-model="dataForm.fromUser" placeholder="举报者Id"></el-input>
      </el-form-item>
          <el-form-item label="被举报者ID" prop="toUser">
          <el-input v-model="dataForm.toUser" placeholder="被举报者ID"></el-input>
      </el-form-item>
          <el-form-item label="举报原因(0:诱导欺骗送礼物1诱导去其他平台2聊天内容涉黄3诱导涉黄4语言暴力/骚扰)" prop="reasons">
          <el-input v-model="dataForm.reasons" placeholder="举报原因(0:诱导欺骗送礼物1诱导去其他平台2聊天内容涉黄3诱导涉黄4语言暴力/骚扰)"></el-input>
      </el-form-item>
          <el-form-item label="举报时间" prop="createTime">
          <el-input v-model="dataForm.createTime" placeholder="举报时间"></el-input>
      </el-form-item>
          <el-form-item label="举报截屏的URL,多条以^分割" prop="screenShot">
          <el-input v-model="dataForm.screenShot" placeholder="举报截屏的URL,多条以^分割"></el-input>
      </el-form-item>
          <el-form-item label="被举报的帖子ID" prop="postId">
          <el-input v-model="dataForm.postId" placeholder="被举报的帖子ID"></el-input>
      </el-form-item>
          <el-form-item label="被举报的圈子ID" prop="circleId">
          <el-input v-model="dataForm.circleId" placeholder="被举报的圈子ID"></el-input>
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
        status: '',
        fromUser: '',
        toUser: '',
        reasons: '',
        createTime: '',
        screenShot: '',
        postId: '',
        circleId: ''
      }
    }
  },
  computed: {
    dataRule () {
      return {
        status: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        fromUser: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        toUser: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        reasons: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        createTime: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        screenShot: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        postId: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        circleId: [
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
      this.$http.get(`/report/report/${this.dataForm.id}`).then(({ data: res }) => {
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
        this.$http[!this.dataForm.id ? 'post' : 'put']('/report/report/', this.dataForm).then(({ data: res }) => {
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
