<template>
  <el-card shadow="never" class="aui-card--fill">
    <div class="mod-flowRecord__flowrecord}">
      <el-form :inline="true" :model="dataForm" @keyup.enter.native="getDataList()">
        <el-form-item>
          <el-input v-model="dataForm.id" placeholder="id" clearable></el-input>
        </el-form-item>
        <el-form-item>
          <el-input v-model="dataForm.code" placeholder="订单号" clearable></el-input>
        </el-form-item>
        <el-form-item>
          <el-input v-model="dataForm.awayUserId" placeholder="收入用户ID" clearable></el-input>
        </el-form-item>
        <el-form-item>
          <el-input v-model="dataForm.outUserId" placeholder="支出用户ID" clearable></el-input>
        </el-form-item>
        <el-form-item>
          <el-button @click="getDataList()">{{ $t('query') }}</el-button>
        </el-form-item>
        <el-form-item>
          <el-button type="info" @click="exportHandle()">{{ $t('export') }}</el-button>
        </el-form-item>
        <!-- <el-form-item>
          <el-button v-if="$hasPermission('flowRecord:flowrecord:save')" type="primary" @click="addOrUpdateHandle()">{{ $t('add') }}</el-button>
        </el-form-item> -->
        <el-form-item>
          <el-button v-if="$hasPermission('flowRecord:flowrecord:delete')" type="danger" @click="deleteHandle()">{{ $t('deleteBatch') }}</el-button>
        </el-form-item>
      </el-form>
      <el-table v-loading="dataListLoading" :data="dataList" border @selection-change="dataListSelectionChangeHandle" style="width: 100%;">
        <el-table-column type="selection" header-align="center" align="center" width="50"></el-table-column>
        <el-table-column prop="id" label="主键ID" width="180" header-align="center" align="center"></el-table-column>
        <el-table-column prop="code" label="订单编号" width="180" header-align="center" align="center"></el-table-column>
        <el-table-column prop="amount" label="金币数量" width="130" header-align="center" align="center"></el-table-column>
        <!-- <el-table-column prop="awayNickName" label="收入用户" width="200" header-align="center" align="center"></el-table-column> -->
        <el-table-column prop="awayUserId" label="收入用户" header-align="center" align="center">
          <template slot-scope="scope" class="qz">
            <ren-popover :id="scope.row.awayUserId" :awayNickName="scope.row.awayNickName" :avatar="scope.row.awayAvatar" ></ren-popover>
          </template>
        </el-table-column>
        <el-table-column prop="awayFrontBalance" label="收入前余额" width="170" header-align="center" align="center"></el-table-column>
        <el-table-column prop="awayAfterBalance" label="收入后余额" width="170" header-align="center" align="center"></el-table-column>

        <!-- <el-table-column prop="outNickName" label="支出用户" width="200" header-align="center" align="center"></el-table-column> -->
        <el-table-column prop="outUserId" label="支出用户" header-align="center" align="center">
          <template slot-scope="scope" class="qz">
            <ren-popover :id="scope.row.outUserId" :outNickName="scope.row.outNickName" :avatar="scope.row.outAvatar" ></ren-popover>
          </template>
        </el-table-column>
        <el-table-column prop="outFrontBalance" label="支出前余额" width="170" header-align="center" align="center"></el-table-column>
        <el-table-column prop="outAfterBalance" label="支出后余额" width="170" header-align="center" align="center"></el-table-column>
        <el-table-column prop="status" label="类型" :show-overflow-tooltip="true" width="100" header-align="center" align="center">
          <template slot-scope="scope">
            <el-tag v-if="scope.row.status === 0" type="warning">打赏</el-tag>
            <el-tag v-if="scope.row.status === 1" type="success">付费圈</el-tag>
            <el-tag v-if="scope.row.status === 2" type="info">付费贴</el-tag>
            <el-tag v-if="scope.row.status === 3" type="danger">发红包</el-tag>
            <el-tag v-if="scope.row.status === 4" type="danger">抢红包</el-tag>
            <el-tag v-if="scope.row.status === 5" type="danger">红包退回</el-tag>
            <el-tag v-if="scope.row.status === 6" type="danger">签到</el-tag>
            <el-tag v-if="scope.row.status === 7" type="danger">抽成</el-tag>
            <el-tag v-if="scope.row.status === 8" type="danger">赞赏</el-tag>
          </template>
        </el-table-column>
        <el-table-column prop="currentId" label="通用ID" width="180" header-align="center" align="center"></el-table-column>
        <el-table-column prop="time" label="时间" width="180" header-align="center" align="center"></el-table-column>
        <el-table-column :label="$t('handle')" fixed="right" header-align="center" align="center" width="150">
          <template slot-scope="scope">
            <!-- <el-button v-if="$hasPermission('flowRecord:flowrecord:update')" type="text" size="small" @click="addOrUpdateHandle(scope.row.id)">{{ $t('update') }}</el-button> -->
            <el-button v-if="$hasPermission('flowRecord:flowrecord:delete')" type="text" size="small" @click="deleteHandle(scope.row.id)">{{ $t('delete') }}</el-button>
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
import AddOrUpdate from './flowrecord-add-or-update'
import RenPopover from "../../../components/ren-popover/ren-popover.vue"
export default {
  mixins: [mixinViewModule],
  data () {
  
    return {
      mixinViewModuleOptions: {
        getDataListURL: '/flowRecord/flowrecord/page',
        getDataListIsPage: true,
        exportURL: '/flowRecord/flowrecord/export',
        deleteURL: '/flowRecord/flowrecord',
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
