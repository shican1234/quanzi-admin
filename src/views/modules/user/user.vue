<template>
  <el-card shadow="never" class="aui-card--fill">
    <div class="mod-user__user}">
      <el-form :inline="true" :model="dataForm" @keyup.enter.native="getDataList()">
        <el-form-item>
          <el-input v-model="dataForm.id" placeholder="id" clearable></el-input>
        </el-form-item>
        <el-form-item>
          <el-input v-model="dataForm.nickName" placeholder="昵称" clearable></el-input>
        </el-form-item>
        <el-form-item>
          <el-input v-model="dataForm.email" placeholder="邮箱" clearable></el-input>
        </el-form-item>
        <el-form-item>
          <el-input v-model="dataForm.mobile" placeholder="手机号" clearable></el-input>
        </el-form-item>
        <el-form-item>
          <el-input v-model="dataForm.pid" placeholder="上级ID" clearable></el-input>
        </el-form-item>
        <el-form-item>
          <el-button @click="getDataList()">{{ $t('query') }}</el-button>
        </el-form-item>
        <el-form-item>
          <el-button type="info" @click="exportHandle()">{{ $t('export') }}</el-button>
        </el-form-item>
        <el-form-item>
          <el-button v-if="$hasPermission('user:user:save')" type="primary" @click="addOrUpdateHandle()">{{ $t('add') }}</el-button>
        </el-form-item>
        <el-form-item>
          <el-button v-if="$hasPermission('user:user:delete')" type="danger" @click="deleteHandle()">{{ $t('deleteBatch') }}</el-button>
        </el-form-item>
      </el-form>
      <el-table v-loading="dataListLoading" :data="dataList" border @selection-change="dataListSelectionChangeHandle" style="width: 100%;">
        <el-table-column type="selection" header-align="center" align="center" width="50"></el-table-column>
        <el-table-column prop="id" label="id" header-align="center" align="center"></el-table-column>
        <el-table-column prop="avatar" label="用户" header-align="center" align="center">
          <template slot-scope="scope" >
            <ren-popover :id="scope.row.id" :nickName="scope.row.nickName" :avatar="scope.row.avatar" ></ren-popover>
          </template>
        </el-table-column>
        <el-table-column prop="cion" label="金币数量" header-align="center" align="center"></el-table-column>
        <el-table-column prop="balance" label="提现余额" header-align="center" align="center"></el-table-column>
        <el-table-column prop="vipDate" label="VIP到期时间" header-align="center" align="center"></el-table-column>
        <el-table-column prop="wxOpenid" label="微信openid" header-align="center" align="center"></el-table-column>
        <el-table-column prop="wxUnionid" label="微信Unionid" header-align="center" align="center"></el-table-column>
        <el-table-column prop="pid" label="上級ID" header-align="center" align="center"></el-table-column>
        <el-table-column prop="email" label="邮箱" header-align="center" align="center"></el-table-column>
        <el-table-column prop="mobile" label="手机号" header-align="center" align="center"></el-table-column>
        <el-table-column prop="gender" label="性别" header-align="center" align="center">
          <template slot-scope="scope">
            <el-tag v-if="scope.row.gender == 0" type="success">男</el-tag>
            <el-tag v-if="scope.row.gender == 1" type="error">女</el-tag>
            <el-tag v-if="scope.row.gender == 2" type="error">未知</el-tag>
            </template>
        </el-table-column>
        <el-table-column prop="birth" label="生日" header-align="center" align="center"></el-table-column>
        <el-table-column prop="emotion" label="婚姻状态" header-align="center" align="center"></el-table-column>
        <el-table-column prop="job" label="职位" header-align="center" align="center"></el-table-column>
        <el-table-column prop="city" label="城市" header-align="center" align="center"></el-table-column>
        <el-table-column prop="postProportion" label="圈子内付费贴分成比例" header-align="center" align="center"></el-table-column>
        <el-table-column prop="withdrawalProportion" label="提现比例" header-align="center" align="center"></el-table-column>
        <el-table-column prop="oneProportion" label="一级返利比例" header-align="center" align="center"></el-table-column>
        <el-table-column prop="twoProportion" label="二级返利比例" header-align="center" align="center"></el-table-column>
        <el-table-column prop="status" label="用户状态" header-align="center" align="center">
          <template slot-scope="scope">
            <el-button v-if="scope.row.status == 0" type="success" size="mini" @click="userLockClick(1,scope.row.id)">正常</el-button>
            <el-button v-if="scope.row.status == 1" type="danger" size="mini" @click="userLockClick(0,scope.row.id)">封禁</el-button>
            </template>
        </el-table-column>
        <el-table-column prop="lockReason" label="封禁原因" header-align="center" align="center"></el-table-column>
        <el-table-column prop="level" label="等级" header-align="center" align="center"></el-table-column>
        <el-table-column prop="experience" label="经验" header-align="center" align="center"></el-table-column>
        <el-table-column prop="bgImg" label="个人中心背景图" header-align="center" align="center">
          <template slot-scope="scope">
            <img v-if="scope.row.bgImg" :src="scope.row.bgImg" width="60" height="60" alt="">
            <span v-else>暂未设置</span>
          </template>
        </el-table-column>
        <el-table-column prop="constellation" label="星座" header-align="center" align="center"></el-table-column>
        <el-table-column prop="loginIp" label="IP" header-align="center" align="center"></el-table-column>
        <el-table-column prop="lastLoginTime" label="最后活跃时间" header-align="center" align="center"></el-table-column>
        <el-table-column prop="signature" label="个性签名" header-align="center" align="center"></el-table-column>
        <el-table-column :label="$t('handle')" fixed="right" header-align="center" align="center" width="150">
          <template slot-scope="scope">
            <el-button v-if="$hasPermission('user:user:update')" type="text" size="small" @click="addOrUpdateHandle(scope.row.id)">{{ $t('update') }}</el-button>
            <el-button v-if="$hasPermission('user:user:delete')" type="text" size="small" @click="deleteHandle(scope.row.id)">{{ $t('delete') }}</el-button>
          </template>
        </el-table-column>
      </el-table>
      <el-pagination
        :current-page="page"
        :page-sizes="[10, 20, 50, 100]"
        :page-size="limit"
        :total="total"
        layout="total, sizes, prev, pager, next, jumper"
        @size-change="pageSizeChangeHandle"
        @current-change="pageCurrentChangeHandle">
      </el-pagination>
      <!-- 弹窗, 新增 / 修改 -->
      <add-or-update v-if="addOrUpdateVisible" ref="addOrUpdate" @refreshDataList="getDataList"></add-or-update>
      <el-dialog
        title="提示"
        :visible.sync="centerDialogVisible"
        width="30%"
        center>
        <el-input v-model="lockUserData.lockReason" placeholder="请输入封禁理由" clearable></el-input>
        <span slot="footer" class="dialog-footer">
          <el-button @click="centerDialogVisible = false">取 消</el-button>
          <el-button type="primary" @click="userLock()">确 定</el-button>
        </span>
      </el-dialog>
    </div>
  </el-card>
</template>

<script>
import mixinViewModule from '@/mixins/view-module'
import AddOrUpdate from './user-add-or-update'
import RenPopover from "../../../components/ren-popover/ren-popover.vue"
export default {
  mixins: [mixinViewModule],
  data () {
    return {
      mixinViewModuleOptions: {
        getDataListURL: '/user/user/page',
        getDataListIsPage: true,
        exportURL: '/user/user/export',
        deleteURL: '/user/user',
        deleteIsBatch: true
      },
      centerDialogVisible: false,
      lockUserData: {
        status: '',
        id: '',
        lockReason: ''
      },
      dataForm: {
        id: '',
        nickName: '',
        email: '',
        mobile: '',
        pid: ''
      }
    }
  },
  components: {
    AddOrUpdate,
    RenPopover
  },
  methods: {
    
    userLockClick(status,id){
      this.lockUserData.userId = id
      this.lockUserData.status = status
      if(status == 1){
        this.centerDialogVisible = true
      }else{
        this.userLock()
      }
    },

    userLock(){
      this.$http.post(`/user/user/userLock`,this.lockUserData).then(({ data: res }) => {
        if (res.code !== 0) {
          return this.$message.error(res.msg)
        }
        if(this.centerDialogVisible){
          this.centerDialogVisible = false
        }
        this.getDataList()
      }).catch(() => {})
    }
  }
}
</script>
