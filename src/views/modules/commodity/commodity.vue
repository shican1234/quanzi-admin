<template>
  <el-card shadow="never" class="aui-card--fill">
    <div class="mod-commodity__commodity}">
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
          <el-button v-if="$hasPermission('commodity:commodity:save')" type="primary" @click="addOrUpdateHandle()">{{ $t('add') }}</el-button>
        </el-form-item>
        <el-form-item>
          <el-button v-if="$hasPermission('commodity:commodity:delete')" type="danger" @click="deleteHandle()">{{ $t('deleteBatch') }}</el-button>
        </el-form-item>
      </el-form>
      <el-table v-loading="dataListLoading" :data="dataList" border @selection-change="dataListSelectionChangeHandle" style="width: 100%;">
        <el-table-column type="selection" header-align="center" align="center" width="50"></el-table-column>
        <el-table-column prop="id" label="主键ID" width="180" header-align="center" align="center"></el-table-column>
        <el-table-column prop="duration" label="显示名称" width="150" header-align="center" align="center"></el-table-column>
        <el-table-column prop="price" label="商品价格" width="150" header-align="center" align="center"></el-table-column>
        <el-table-column prop="commoditySpecification" width="150" label="商品规格" header-align="center" align="center"></el-table-column>
        <el-table-column prop="commodityToSort" label="商品排序" header-align="center" align="center"></el-table-column>
        <!-- <el-table-column prop="type" label="商品类型(1:钻石  2:VIP)" header-align="center" align="center"></el-table-column> -->
        <el-table-column prop="type" label="商品类型" header-align="center" align="center">
          <template slot-scope="scope">
            <el-tag v-if="scope.row.type === 1">钻石</el-tag>
            <el-tag v-if="scope.row.type === 2" size="success" type="success">VIP</el-tag>
          </template>
        </el-table-column>
        <!-- <el-table-column prop="status" label="是否开启(1:是  2:否)" header-align="center" align="center"></el-table-column> -->
        <el-table-column prop="status" label="是否使用" header-align="center" align="center">
          <template slot-scope="scope">
            <el-tag v-if="scope.row.status === 1" size="success" type="success">是</el-tag>
            <el-tag v-if="scope.row.status === 2" size="warning" type="danger">否</el-tag>
          </template>
        </el-table-column>
        <el-table-column prop="label" label="标签状态" header-align="center" align="center">
          <template slot-scope="scope">
            <el-tag v-if="scope.row.label === 0" type="success">使用</el-tag>
            <el-tag v-if="scope.row.label === 1">关闭</el-tag>
          </template>
        </el-table-column>
        <el-table-column prop="briefIntroduction" label="商品简介" :show-overflow-tooltip="true" width="150" header-align="center" align="center"></el-table-column>
        <el-table-column prop="time" label="创建时间" width="180" header-align="center" align="center"></el-table-column>
        <el-table-column :label="$t('handle')" fixed="right" header-align="center" align="center" width="150">
          <template slot-scope="scope">
            <el-button v-if="$hasPermission('commodity:commodity:update')" type="text" size="small" @click="addOrUpdateHandle(scope.row.id)">{{ $t('update') }}</el-button>
            <el-button v-if="$hasPermission('commodity:commodity:delete')" type="text" size="small" @click="deleteHandle(scope.row.id)">{{ $t('delete') }}</el-button>
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
import AddOrUpdate from './commodity-add-or-update'
export default {
  mixins: [mixinViewModule],
  data () {
    return {
      mixinViewModuleOptions: {
        getDataListURL: '/commodity/commodity/page',
        getDataListIsPage: true,
        exportURL: '/commodity/commodity/export',
        deleteURL: '/commodity/commodity',
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
