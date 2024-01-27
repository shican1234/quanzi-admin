<template>
  <el-card shadow="never" class="aui-card--fill">
    <div class="mod-payConfig__payconfig}">
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
          <el-button v-if="$hasPermission('payConfig:payconfig:save')" type="primary" @click="addOrUpdateHandle()">{{ $t('add') }}</el-button>
        </el-form-item>
        <el-form-item>
          <el-button v-if="$hasPermission('payConfig:payconfig:delete')" type="danger" @click="deleteHandle()">{{ $t('deleteBatch') }}</el-button>
        </el-form-item>
      </el-form>
      <el-table v-loading="dataListLoading" :data="dataList" border @selection-change="dataListSelectionChangeHandle" style="width: 100%;">
        <el-table-column type="selection" header-align="center" align="center" width="50"></el-table-column>
        <el-table-column prop="id" label="主键ID" :show-overflow-tooltip="true" width="200" header-align="center" align="center"></el-table-column>
        <el-table-column prop="merchantsName" label="商户名称" :show-overflow-tooltip="true" width="200" header-align="center" align="center"></el-table-column>
        <el-table-column prop="payName" label="支付公司名称" :show-overflow-tooltip="true" width="200" header-align="center" align="center"></el-table-column>
        <!-- <el-table-column prop="peyType" label="支付类型(1:支付宝-官方  2:微信-官方  3:支付宝-三方  4:微信-三方)" header-align="center" align="center"></el-table-column> -->
        <el-table-column prop="paySource" label="所属渠道" :show-overflow-tooltip="true" width="100" header-align="center" align="center">
          <template slot-scope="scope">
            <el-tag v-if="scope.row.paySource === 1" >原生</el-tag>
            <el-tag v-if="scope.row.paySource === 2" size="success" type="success">三方</el-tag>
          </template>
        </el-table-column>

        <el-table-column prop="peyType" label="支付类型" :show-overflow-tooltip="true" width="100" header-align="center" align="center">
          <template slot-scope="scope">
            <el-tag v-if="scope.row.peyType === 1" >支付宝</el-tag>
            <el-tag v-if="scope.row.peyType === 2" size="success" type="success">微信</el-tag>
          </template>
        </el-table-column>
        <el-table-column prop="peyStatus" label="拉起方式" :show-overflow-tooltip="true" width="100" header-align="center" align="center">
          <template slot-scope="scope">
            <el-tag v-if="scope.row.peyStatus === 1" size="success" type="success">APP</el-tag>
            <el-tag v-if="scope.row.peyStatus === 2" size="success" type="warning">H5</el-tag>
            <el-tag v-if="scope.row.peyStatus === 3" size="success" type="danger">小程序</el-tag>
            <el-tag v-if="scope.row.peyStatus === 4" size="success" type="danger">公众号</el-tag>
          </template>
        </el-table-column>
        <!-- <el-table-column prop="status" label="是否开启(1:开启  2:关闭)" header-align="center" align="center"></el-table-column> -->
        <el-table-column prop="status" label="是否开启" :show-overflow-tooltip="true" width="100" header-align="center" align="center">
          <template slot-scope="scope">
            <el-tag v-if="scope.row.status === 1" size="success" type="success">开启</el-tag>
            <el-tag v-if="scope.row.status === 2" size="success" type="danger">关闭</el-tag>
          </template>
        </el-table-column>
        <el-table-column prop="configurationJson" label="支付配置" :show-overflow-tooltip="true" width="200" header-align="center" align="center"></el-table-column>
        <el-table-column prop="remark" label="备注" :show-overflow-tooltip="true" width="200" header-align="center" align="center"></el-table-column>
        <el-table-column prop="showName" label="显示名称" :show-overflow-tooltip="true" width="130" header-align="center" align="center"></el-table-column>
        <el-table-column prop="callName" label="调用名称" :show-overflow-tooltip="true" width="130" header-align="center" align="center"></el-table-column>
        <el-table-column prop="time" label="创建时间" width="180" header-align="center" align="center"></el-table-column>
        <el-table-column :label="$t('handle')" fixed="right" header-align="center" align="center" width="150">
          <template slot-scope="scope">
            <el-button v-if="$hasPermission('payConfig:payconfig:update')" type="text" size="small" @click="addOrUpdateHandle(scope.row.id)">{{ $t('update') }}</el-button>
            <el-button v-if="$hasPermission('payConfig:payconfig:delete')" type="text" size="small" @click="deleteHandle(scope.row.id)">{{ $t('delete') }}</el-button>
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
import AddOrUpdate from './payconfig-add-or-update'
export default {
  mixins: [mixinViewModule],
  data () {
    return {
      mixinViewModuleOptions: {
        getDataListURL: '/payConfig/payconfig/page',
        getDataListIsPage: true,
        exportURL: '/payConfig/payconfig/export',
        deleteURL: '/payConfig/payconfig',
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
