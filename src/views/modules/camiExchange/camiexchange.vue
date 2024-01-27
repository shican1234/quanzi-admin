<template>
  <el-card shadow="never" class="aui-card--fill">
    <div class="mod-camiExchange__camiexchange}">
      <el-form :inline="true" :model="dataForm" @keyup.enter.native="getDataList()">
        <el-form-item>
          <el-input v-model="dataForm.id" placeholder="id" clearable></el-input>
        </el-form-item>
        <el-form-item>
          <el-input v-model="dataForm.userId" placeholder="兑换用户ID" clearable></el-input>
        </el-form-item>
        <el-form-item>
          <el-input v-model="dataForm.camiCode" placeholder="卡密号" clearable></el-input>
        </el-form-item>
        <el-form-item>
          <el-button @click="getDataList()">{{ $t('query') }}</el-button>
        </el-form-item>
        <el-form-item>
          <el-button type="info" @click="exportHandle()">{{ $t('export') }}</el-button>
        </el-form-item>
        
        
      </el-form>
      <el-table v-loading="dataListLoading" :data="dataList" border @selection-change="dataListSelectionChangeHandle" style="width: 100%;">
        <el-table-column type="selection" header-align="center" align="center" width="50"></el-table-column>
        <!-- <el-table-column prop="memberNichen" label="用户昵称" header-align="center" width="200" align="center"></el-table-column> -->
        <el-table-column prop="userId" label="兑换用户" header-align="center" align="center">
          <template slot-scope="scope" class="qz">
            <ren-popover :id="scope.row.userId" :memberNichen="scope.row.memberNichen" :avatar="scope.row.avatar" ></ren-popover>
          </template>
        </el-table-column>
        <el-table-column prop="camiCode" label="卡密号" :show-overflow-tooltip="true" width="200" header-align="center" align="center"></el-table-column>
        <el-table-column prop="price" label="卡密价格" :show-overflow-tooltip="true" width="150" header-align="center" align="center"></el-table-column>
        <el-table-column prop="specification" label="卡密规格" header-align="center" align="center"></el-table-column>
        <!-- <el-table-column prop="type" label="卡密兑换类型(0:VIP  1:金币)" header-align="center" align="center"></el-table-column> -->
        <el-table-column prop="type" label="兑换类型" header-align="center" align="center">
          <template slot-scope="scope">
            <el-tag v-if="scope.row.type === 0">VIP</el-tag>
            <el-tag v-if="scope.row.type === 1" size="success" type="success">钻石</el-tag>
          </template>
        </el-table-column>
        <el-table-column prop="time" label="兑换时间" :show-overflow-tooltip="true" width="200" header-align="center" align="center"></el-table-column>
        <el-table-column :label="$t('handle')" fixed="right" header-align="center" align="center" width="150">
          <template slot-scope="scope">
            <!-- <el-button v-if="$hasPermission('camiExchange:camiexchange:update')" type="text" size="small" @click="addOrUpdateHandle(scope.row.id)">{{ $t('update') }}</el-button> -->
            <el-button v-if="$hasPermission('camiExchange:camiexchange:delete')" type="text" size="small" @click="deleteHandle(scope.row.id)">{{ $t('delete') }}</el-button>
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
import AddOrUpdate from './camiexchange-add-or-update'
import RenPopover from "../../../components/ren-popover/ren-popover.vue"
export default {
  mixins: [mixinViewModule],
  data () {
    return {
      mixinViewModuleOptions: {
        getDataListURL: '/camiExchange/camiexchange/page',
        getDataListIsPage: true,
        exportURL: '/camiExchange/camiexchange/export',
        deleteURL: '/camiExchange/camiexchange',
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
