<template>
  <el-card shadow="never" class="aui-card--fill">
    <div class="mod-InviteRebate__inviterebate}">
      <el-form :inline="true" :model="dataForm" @keyup.enter.native="getDataList()">
        <el-form-item>
          <el-input v-model="dataForm.id" placeholder="id" clearable></el-input>
        </el-form-item>
        <el-form-item>
          <el-input v-model="dataForm.orderId" placeholder="充值订单ID" clearable></el-input>
        </el-form-item>
        <el-form-item>
          <el-input v-model="dataForm.code" placeholder="订单号" clearable></el-input>
        </el-form-item>
        <el-form-item>
          <el-input v-model="dataForm.userId" placeholder="收入用户ID" clearable></el-input>
        </el-form-item>
        <el-form-item>
          <el-input v-model="dataForm.subordinateId" placeholder="下级用户ID" clearable></el-input>
        </el-form-item>
        <el-form-item>
          <el-button @click="getDataList()">{{ $t('query') }}</el-button>
        </el-form-item>
        <el-form-item>
          <el-button type="info" @click="exportHandle()">{{ $t('export') }}</el-button>
        </el-form-item>
        <el-form-item>
          <el-button v-if="$hasPermission('InviteRebate:inviterebate:save')" type="primary" @click="addOrUpdateHandle()">{{ $t('add') }}</el-button>
        </el-form-item>
        <el-form-item>
          <el-button v-if="$hasPermission('InviteRebate:inviterebate:delete')" type="danger" @click="deleteHandle()">{{ $t('deleteBatch') }}</el-button>
        </el-form-item>
      </el-form>
      <el-table v-loading="dataListLoading" :data="dataList" border @selection-change="dataListSelectionChangeHandle" style="width: 100%;">
        <el-table-column type="selection" header-align="center" align="center" width="50"></el-table-column>
        <el-table-column prop="orderId" label="订单ID" width="180" header-align="center" align="center"></el-table-column>
        <el-table-column prop="code" label="订单号" width="210" header-align="center" align="center"></el-table-column>
        <!-- <el-table-column prop="userId" label="用户ID" width="180" header-align="center" align="center"></el-table-column> -->
        <el-table-column prop="userId" label="收入用户" width="100" header-align="center" align="center">
          <template slot-scope="scope" class="qz">
            <ren-popover :id="scope.row.userId" :nickName="scope.row.nickName" :avatar="scope.row.avatar" ></ren-popover>
          </template>
        </el-table-column>

        <!-- <el-table-column prop="subordinateId" label="下级ID" width="180" header-align="center" align="center"></el-table-column> -->
        <el-table-column prop="subordinateId" label="下级用户" header-align="center" align="center">
          <template slot-scope="scope" class="qz">
            <ren-popover :id="scope.row.subordinateId" :xnickName="scope.row.xnickName" :avatar="scope.row.xjAvatar" ></ren-popover>
          </template>
        </el-table-column>
        <el-table-column prop="lowerLevel" label="来自下级" header-align="center" align="center"></el-table-column>
        <el-table-column prop="totalAmount" label="充值金额" header-align="center" align="center"></el-table-column>
        <el-table-column prop="totalSpecies" label="返利金币" header-align="center" align="center"></el-table-column>
        <el-table-column prop="obtainAmount" label="返利金额" header-align="center" align="center"></el-table-column>
        <el-table-column prop="proportion" label="返利比例" header-align="center" align="center"></el-table-column>
        <el-table-column prop="frontBalance" label="返利前余额" width="180" header-align="center" align="center"></el-table-column>
        <el-table-column prop="afterBalance" label="返利后余额" width="180" header-align="center" align="center"></el-table-column>
        <el-table-column prop="time" label="返利时间" width="180" header-align="center" align="center"></el-table-column>
        <el-table-column :label="$t('handle')" fixed="right" header-align="center" align="center" width="150">
          <template slot-scope="scope">
            <el-button v-if="$hasPermission('InviteRebate:inviterebate:update')" type="text" size="small" @click="addOrUpdateHandle(scope.row.id)">{{ $t('update') }}</el-button>
            <el-button v-if="$hasPermission('InviteRebate:inviterebate:delete')" type="text" size="small" @click="deleteHandle(scope.row.id)">{{ $t('delete') }}</el-button>
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
    </div>
  </el-card>
</template>

<script>
import mixinViewModule from '@/mixins/view-module'
import AddOrUpdate from './inviterebate-add-or-update'
import RenPopover from "../../../components/ren-popover/ren-popover.vue"
export default {
  mixins: [mixinViewModule],
  data () {
    return {
      mixinViewModuleOptions: {
        getDataListURL: '/InviteRebate/inviterebate/page',
        getDataListIsPage: true,
        exportURL: '/InviteRebate/inviterebate/export',
        deleteURL: '/InviteRebate/inviterebate',
        deleteIsBatch: true
      },
      dataForm: {
        id: ''
      }
    }
  },
  components: {
    AddOrUpdate,
    RenPopover
  }
}
</script>
