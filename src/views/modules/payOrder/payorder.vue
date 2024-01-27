<template>
  <el-card shadow="never" class="aui-card--fill">
    <div class="mod-payOrder__payorder}">
      <el-form :inline="true" :model="dataForm" @keyup.enter.native="getDataList()">
        <el-form-item>
          <el-input v-model="dataForm.id" placeholder="id" clearable></el-input>
        </el-form-item>
        <el-form-item>
          <el-input v-model="dataForm.userId" placeholder="用户ID" clearable></el-input>
        </el-form-item>
        <el-form-item>
          <el-select v-model="dataForm.peyType" placeholder="支付类型">
            <el-option  label="所有" value=''></el-option>
            <el-option :key=1 label="支付宝" :value=1></el-option>
            <el-option :key=2 label="微信" :value=2></el-option>
          </el-select>
        </el-form-item>
        <el-form-item>
          <el-select v-model="dataForm.peyStatus" placeholder="拉起方式">
            <el-option  label="所有" value=''></el-option>
            <el-option :key=1 label="APP" :value=1></el-option>
            <el-option :key=2 label="H5" :value=2></el-option>
            <el-option :key=3 label="小程序" :value=3></el-option>
          </el-select>
        </el-form-item>
        <el-form-item>
          <el-select v-model="dataForm.commodityType" placeholder="商品类型">
            <el-option  label="所有" value=''></el-option>
            <el-option :key=1 label="钻石" :value=1></el-option>
            <el-option :key=2 label="VIP" :value=2></el-option>
          </el-select>
        </el-form-item>
          <el-form-item><el-input v-model="dataForm.orderCode" placeholder="平台订单号" clearable></el-input></el-form-item>
          <el-form-item> <el-input v-model="dataForm.trilateralCode" placeholder="三方订单号" clearable></el-input></el-form-item>
        <el-form-item>
          <el-button @click="getDataList()">{{ $t('query') }}</el-button>
        </el-form-item>
        <el-form-item>
          <el-button type="info" @click="exportHandle()">{{ $t('export') }}</el-button>
        </el-form-item>
        <el-form-item>
          <el-button v-if="$hasPermission('payOrder:payorder:save')" type="primary" @click="addOrUpdateHandle()">{{ $t('add') }}</el-button>
        </el-form-item>
        <el-form-item>
          <el-button v-if="$hasPermission('payOrder:payorder:delete')" type="danger" @click="deleteHandle()">{{ $t('deleteBatch') }}</el-button>
        </el-form-item>
      </el-form>
      <el-table v-loading="dataListLoading" :data="dataList" border @selection-change="dataListSelectionChangeHandle" style="width: 100%;">
        <el-table-column type="selection" header-align="center" align="center" width="50"></el-table-column>
        <el-table-column prop="id" label="主键ID" width="180" header-align="center" align="center"></el-table-column>
        <el-table-column prop="userId" label="用户" header-align="center" align="center">
          <template slot-scope="scope" class="qz">
            <ren-popover :id="scope.row.userId" :memberNichen="scope.row.userName" :avatar="scope.row.avatar" ></ren-popover>
          </template>
        </el-table-column>
        <el-table-column prop="commodityName" label="商品名称" width="180" header-align="center" align="center"></el-table-column>
        <!-- <el-table-column prop="commodityType" label="充值商品类型(1:钻石  2:VIP)" header-align="center" align="center"></el-table-column> -->
        <el-table-column prop="commodityType" label="商品类型" header-align="center" align="center">
          <template slot-scope="scope">
            <el-tag v-if="scope.row.commodityType === 1">钻石</el-tag>
            <el-tag v-if="scope.row.commodityType === 2" size="success" type="success">VIP</el-tag>
          </template>
        </el-table-column>
        <el-table-column prop="commoditySpecification" width="130" label="商品规格" header-align="center" align="center"></el-table-column>
        <!-- <el-table-column prop="peyType" label="支付类型(1:支付宝  2:微信)" header-align="center" align="center"></el-table-column> -->
        <el-table-column prop="peyType" label="支付类型" :show-overflow-tooltip="true" width="100" header-align="center" align="center">
          <template slot-scope="scope">
            <el-tag v-if="scope.row.peyType === 1" >支付宝</el-tag>
            <el-tag v-if="scope.row.peyType === 2" size="success" type="success">微信</el-tag>
          </template>
        </el-table-column>
        <!-- <el-table-column prop="peyStatus" label="拉起方式(1:APP  2:H5  3:小程序)" header-align="center" align="center"></el-table-column> -->
        <el-table-column prop="peyStatus" label="拉起方式" :show-overflow-tooltip="true" width="100" header-align="center" align="center">
          <template slot-scope="scope">
            <el-tag v-if="scope.row.peyStatus === 1" size="success" type="success">APP</el-tag>
            <el-tag v-if="scope.row.peyStatus === 2" size="success" type="warning">H5</el-tag>
            <el-tag v-if="scope.row.peyStatus === 3" size="success" type="danger">小程序</el-tag>
            <el-tag v-if="scope.row.peyStatus === 4" size="success" type="danger">公众号</el-tag>
          </template>
        </el-table-column>
        <el-table-column prop="orderCode" label="平台订单号" width="200" header-align="center" align="center"></el-table-column>
        <el-table-column prop="trilateralCode" label="三方订单号" width="200" header-align="center" align="center"></el-table-column>
        <el-table-column prop="rechargeAmount" label="充值金额" header-align="center" align="center"></el-table-column>
        <!-- <el-table-column prop="orderType" label="订单支付状态(0:订单创建  1:支付成功  2:支付失败  3:订单超时)" header-align="center" align="center"></el-table-column> -->
        <el-table-column prop="orderType" label="支付状态" :show-overflow-tooltip="true" width="100" header-align="center" align="center">
          <template slot-scope="scope">
            <el-tag v-if="scope.row.orderType === 0" type="warning">订单创建</el-tag>
            <el-tag v-if="scope.row.orderType === 1" size="success" type="success">支付成功</el-tag>
            <el-tag v-if="scope.row.orderType === 2" size="success" type="info">支付失败</el-tag>
            <el-tag v-if="scope.row.orderType === 3" size="success" type="danger">订单超时</el-tag>
          </template>
        </el-table-column>
        <el-table-column prop="foundTime" label="订单创建时间" width="200" header-align="center" align="center"></el-table-column>
        <el-table-column :label="$t('handle')" fixed="right" header-align="center" align="center" width="150">
          <template slot-scope="scope">
            <el-button v-if="$hasPermission('payOrder:payorder:update')" type="text" size="small" @click="addOrUpdateHandle(scope.row.id)">{{ $t('check') }}</el-button>
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
import AddOrUpdate from './payorder-add-or-update'
import RenPopover from "../../../components/ren-popover/ren-popover.vue"
export default {
  mixins: [mixinViewModule],
  data () {
    return {
      mixinViewModuleOptions: {
        getDataListURL: '/payOrder/payorder/page',
        getDataListIsPage: true,
        exportURL: '/payOrder/payorder/export',
        deleteURL: '/payOrder/payorder',
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
