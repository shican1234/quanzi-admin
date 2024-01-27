<template>
  <el-dialog :visible.sync="visible" :title="!dataForm.id ? $t('add') : $t('update')" :close-on-click-modal="false" :close-on-press-escape="false">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmitHandle()" :label-width="$i18n.locale === 'en-US' ? '120px' : '80px'">
          <el-form-item label="帖子文字内容" prop="content">
          <el-input v-model="dataForm.content" placeholder="帖子文字内容"></el-input>
      </el-form-item>
          <el-form-item label="文件地址,json格式" prop="fileUrls">
          <el-input v-model="dataForm.fileUrls" placeholder="文件地址,json格式"></el-input>
      </el-form-item>
          <el-form-item label="发帖的人的ID" prop="userId">
          <el-input v-model="dataForm.userId" placeholder="发帖的人的ID"></el-input>
      </el-form-item>
          <el-form-item label="圈子ID" prop="circleId">
          <el-input v-model="dataForm.circleId" placeholder="圈子ID"></el-input>
      </el-form-item>
          <el-form-item label="状态(0审核中1审核通过1拒绝)" prop="status">
          <el-input v-model="dataForm.status" placeholder="状态(0审核中1审核通过1拒绝)"></el-input>
      </el-form-item>
          <el-form-item label="类型(0公开帖子1圈内帖子)" prop="type">
          <el-input v-model="dataForm.type" placeholder="类型(0公开帖子1圈内帖子)"></el-input>
      </el-form-item>
          <el-form-item label="发帖时间" prop="createTime">
          <el-input v-model="dataForm.createTime" placeholder="发帖时间"></el-input>
      </el-form-item>
          <el-form-item label="文件类型(0图片1视频)" prop="fileType">
          <el-input v-model="dataForm.fileType" placeholder="文件类型(0图片1视频)"></el-input>
      </el-form-item>
          <el-form-item label="话题ID" prop="discussId">
          <el-input v-model="dataForm.discussId" placeholder="话题ID"></el-input>
      </el-form-item>
          <el-form-item label="标题" prop="title">
          <el-input v-model="dataForm.title" placeholder="标题"></el-input>
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
        content: '',
        fileUrls: '',
        userId: '',
        circleId: '',
        status: '',
        type: '',
        createTime: '',
        fileType: '',
        discussId: '',
        title: ''
      }
    }
  },
  computed: {
    dataRule () {
      return {
        content: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        fileUrls: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        userId: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        circleId: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        status: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        type: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        createTime: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        fileType: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        discussId: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        title: [
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
      this.$http.get(`/post/post/${this.dataForm.id}`).then(({ data: res }) => {
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
        this.$http[!this.dataForm.id ? 'post' : 'put']('/post/post/', this.dataForm).then(({ data: res }) => {
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
