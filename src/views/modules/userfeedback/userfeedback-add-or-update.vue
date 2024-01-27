<template>
  <el-dialog :visible.sync="visible" :title="!dataForm.id ? $t('add') : $t('update')" :close-on-click-modal="false" :close-on-press-escape="false">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmitHandle()" :label-width="$i18n.locale === 'en-US' ? '120px' : '80px'">
          <el-form-item label="用户ID" prop="userId">
          <el-input v-model="dataForm.userId" placeholder="反馈用户ID" :disabled="!!dataForm.id"></el-input>
      </el-form-item>
          
      <el-form-item label="联系名称" prop="userName">
          <el-input v-model="dataForm.userName" placeholder="联系名称" :disabled="!!dataForm.id"></el-input>
      </el-form-item>
      <el-form-item label="联系方式" prop="contactInformation">
          <el-input v-model="dataForm.contactInformation" placeholder="联系方式" :disabled="!!dataForm.id"></el-input>
      </el-form-item>
      <el-form-item label="反馈内容" prop="feedbackContent" >
          <el-input type="textarea" autosize placeholder="反馈内容" v-model="dataForm.feedbackContent" :disabled="!!dataForm.id">
      </el-input>
      </el-form-item>
      <el-form-item label="反馈时间" prop="feedbackTime">
          <el-input v-model="dataForm.feedbackTime" placeholder="反馈时间" :disabled="!!dataForm.id"></el-input>
      </el-form-item>
      <el-form-item prop="status" label="是否解决" size="mini">
        <el-radio-group v-model="dataForm.status" :disabled="!!dataForm.id">
          <el-radio :label="0">已解决</el-radio>
          <el-radio :label="1">未解决</el-radio>
          <el-radio :label="2">未知</el-radio>
        </el-radio-group>
      </el-form-item>
      <el-form-item prop="type" label="回复状态" size="mini">
        <el-radio-group v-model="dataForm.type" :disabled="!!dataForm.id">
          <el-radio :label="0">未回复</el-radio>
          <el-radio :label="1">已回复</el-radio>
          <el-radio :label="2">未知</el-radio>
        </el-radio-group>
      </el-form-item>
      <el-form-item label="回复内容" prop="replyContent" >
          <el-input type="textarea" autosize placeholder="回复内容" v-model="dataForm.replyContent">
      </el-input>
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
        content: '',
        replyContent: '',
        type: ''
      }
    }
  },
  computed: {
    dataRule () {
      return {
        userId: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        content: [
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
      this.$http.get(`/userfeedback/userfeedback/${this.dataForm.id}`).then(({ data: res }) => {
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
        this.$http[!this.dataForm.id ? 'post' : 'put']('/userfeedback/userfeedback/', this.dataForm).then(({ data: res }) => {
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
