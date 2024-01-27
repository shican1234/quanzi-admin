<template>
  <el-dialog :visible.sync="visible" :title="!dataForm.id ? $t('add') : $t('update')" :close-on-click-modal="false" :close-on-press-escape="false">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmitHandle()" :label-width="$i18n.locale === 'en-US' ? '120px' : '80px'">
          <el-form-item label="所属帖子id" prop="tid">
          <el-input v-model="dataForm.tid" placeholder="所属帖子id"></el-input>
      </el-form-item>
          <el-form-item label="评论人id" prop="commentUserId">
          <el-input v-model="dataForm.commentUserId" placeholder="评论人id"></el-input>
      </el-form-item>
          <el-form-item label="所属评论id，主评论为null" prop="parentId">
          <el-input v-model="dataForm.parentId" placeholder="所属评论id，主评论为null"></el-input>
      </el-form-item>
          <el-form-item label="评论内容" prop="content">
          <el-input v-model="dataForm.content" placeholder="评论内容"></el-input>
      </el-form-item>
          <el-form-item label="点赞(存储点赞人id数组)" prop="like">
          <el-input v-model="dataForm.like" placeholder="点赞(存储点赞人id数组)"></el-input>
      </el-form-item>
          <el-form-item label="状态，0-未审核，1-展现，2-审核驳回，3-已删除" prop="status">
          <el-input v-model="dataForm.status" placeholder="状态，0-未审核，1-展现，2-审核驳回，3-已删除"></el-input>
      </el-form-item>
          <el-form-item label="" prop="createTime">
          <el-input v-model="dataForm.createTime" placeholder=""></el-input>
      </el-form-item>
          <el-form-item label="" prop="updateTime">
          <el-input v-model="dataForm.updateTime" placeholder=""></el-input>
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
        tid: '',
        commentUserId: '',
        parentId: '',
        content: '',
        like: '',
        status: '',
        createTime: '',
        updateTime: ''
      }
    }
  },
  computed: {
    dataRule () {
      return {
        tid: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        commentUserId: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        parentId: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        content: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        like: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        status: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        createTime: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        updateTime: [
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
      this.$http.get(`/comment/comment/${this.dataForm.id}`).then(({ data: res }) => {
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
        this.$http[!this.dataForm.id ? 'post' : 'put']('/comment/comment/', this.dataForm).then(({ data: res }) => {
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
