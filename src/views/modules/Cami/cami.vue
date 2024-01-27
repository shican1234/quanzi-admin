<template>
  <el-card shadow="never" class="aui-card--fill">
    <div class="mod-Cami__cami}">
      <el-form :inline="true" :model="dataForm" @keyup.enter.native="getDataList()">
        <el-form-item>
          <el-input v-model="dataForm.id" placeholder="id" clearable></el-input>
        </el-form-item>
        <el-form-item>
          <el-button @click="getDataList()">{{ $t('query') }}</el-button>
        </el-form-item>
        <el-form-item>
          <el-button type="info" @click="exportHandle()">{{ $t('export') }}</el-button>
        </el-form-item>
        <el-form-item>
          <el-button v-if="$hasPermission('Cami:cami:save')" type="primary" @click="addOrUpdateHandle()">{{ $t('add') }}</el-button>
        </el-form-item>
        <el-form-item>
          <el-button v-if="$hasPermission('Cami:cami:delete')" type="danger" @click="deleteHandle()">{{ $t('deleteBatch') }}</el-button>
        </el-form-item>
      </el-form>
      <el-table v-loading="dataListLoading" :data="dataList" border @selection-change="dataListSelectionChangeHandle" style="width: 100%;">
        <el-table-column type="selection" header-align="center" align="center" width="50"></el-table-column>
        <el-table-column prop="id" label="主键ID" header-align="center" align="center"></el-table-column>
        <el-table-column prop="camiCode" label="卡密号" :show-overflow-tooltip="true" width="200" header-align="center" align="center"></el-table-column>
        <el-table-column prop="price" label="卡密价格" header-align="center" align="center"></el-table-column>
        <el-table-column prop="specification" label="卡密规格" header-align="center" align="center"></el-table-column>
        <!-- <el-table-column prop="type" label="卡密兑换类型(0:VIP  1:金币)" header-align="center" align="center"></el-table-column> -->
        <el-table-column prop="type" label="商品类型" header-align="center" align="center">
          <template slot-scope="scope">
            <el-tag v-if="scope.row.type === 0">VIP</el-tag>
            <el-tag v-if="scope.row.type === 1" size="success" type="success">钻石</el-tag>
          </template>
        </el-table-column>
        <!-- <el-table-column prop="status" label="使用状态(0:未使用  1:已使用)" header-align="center" align="center"></el-table-column> -->
        <el-table-column prop="status" label="是否使用" header-align="center" align="center">
          <template slot-scope="scope">
            <el-tag v-if="scope.row.status === 1" size="success" type="success">未使用</el-tag>
            <el-tag v-if="scope.row.status === 2" size="warning" type="danger">已使用</el-tag>
          </template>
        </el-table-column>
        <el-table-column prop="time" label="生成时间" :show-overflow-tooltip="true" width="150" header-align="center" align="center"></el-table-column>
        <el-table-column prop="exchangeTime" label="兑换时间" :show-overflow-tooltip="true" width="150" header-align="center" align="center"></el-table-column>
        <el-table-column :label="$t('handle')" fixed="right" header-align="center" align="center" width="150">
          <template slot-scope="scope">
            <el-button v-if="$hasPermission('Cami:cami:update')" type="text" size="small" @click="addOrUpdateHandle(scope.row.id)">{{ $t('update') }}</el-button>
            <el-button v-if="$hasPermission('Cami:cami:delete')" type="text" size="small" @click="deleteHandle(scope.row.id)">{{ $t('delete') }}</el-button>
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
import AddOrUpdate from './cami-add-or-update'
export default {
  mixins: [mixinViewModule],
  data () {
    return {
      mixinViewModuleOptions: {
        getDataListURL: '/Cami/cami/page',
        getDataListIsPage: true,
        exportURL: '/Cami/cami/export',
        deleteURL: '/Cami/cami',
        deleteIsBatch: true
      },
      dataForm: {
        id: ''
      }
    }
  },
  components: {
    AddOrUpdate
  }
}
</script>
