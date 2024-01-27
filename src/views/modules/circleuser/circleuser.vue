<template>
  <el-card shadow="never" class="aui-card--fill">
    <div class="mod-circleuser__circleuser}">
      <el-form :inline="true" :model="dataForm" @keyup.enter.native="getDataList()">
        <el-form-item>
          <el-input v-model="dataForm.userId" placeholder="用户ID" clearable></el-input>
        </el-form-item>
        <el-form-item>
          <el-input v-model="dataForm.circleId" placeholder="圈子ID" clearable></el-input>
        </el-form-item>
        <el-form-item>
          <el-button @click="getDataList()">{{ $t('query') }}</el-button>
        </el-form-item>
        <el-form-item>
          <el-button type="info" @click="exportHandle()">{{ $t('export') }}</el-button>
        </el-form-item>
        <el-form-item>
          <el-button v-if="$hasPermission('circleuser:circleuser:save')" type="primary" @click="addOrUpdateHandle()">{{ $t('add') }}</el-button>
        </el-form-item>
        <el-form-item>
          <el-button v-if="$hasPermission('circleuser:circleuser:delete')" type="danger" @click="deleteHandle()">{{ $t('deleteBatch') }}</el-button>
        </el-form-item>
      </el-form>
      <el-table v-loading="dataListLoading" :data="dataList" border @selection-change="dataListSelectionChangeHandle" style="width: 100%;">
        <el-table-column type="selection" header-align="center" align="center" width="50"></el-table-column>
        <el-table-column prop="id" label="id" header-align="center" align="center"></el-table-column>
        <el-table-column prop="userId" label="用户" header-align="center" align="center">
          <template slot-scope="scope" class="qz">
            <ren-popover :id="scope.row.userId" :nickName="scope.row.nickName" :avatar="scope.row.avatar" ></ren-popover>
          </template>
        </el-table-column>
        <el-table-column prop="circleId" label="圈子ID" header-align="center" align="center"></el-table-column>
        <el-table-column prop="circleName" label="圈子名字" header-align="center" align="center"></el-table-column>
       
        <el-table-column prop="createTime" label="入圈时间" header-align="center" align="center"></el-table-column>
        <el-table-column prop="type" label="类型" header-align="center" align="center">
          <template slot-scope="scope">
            <el-tag v-if="scope.row.type == 0" type="success">普通用户</el-tag>
            <el-tag v-if="scope.row.type == 1" type="success">特邀嘉宾</el-tag>
            <el-tag v-if="scope.row.type == 2" type="success">管理员</el-tag>
            <el-tag v-if="scope.row.type == 3" type="success">圈主</el-tag>
            </template>
        </el-table-column>
        <!-- <el-table-column :label="$t('handle')" fixed="right" header-align="center" align="center" width="150">
          <template slot-scope="scope">
            <el-button v-if="$hasPermission('circleuser:circleuser:update')" type="text" size="small" @click="addOrUpdateHandle(scope.row.id)">{{ $t('update') }}</el-button>
            <el-button v-if="$hasPermission('circleuser:circleuser:delete')" type="text" size="small" @click="deleteHandle(scope.row.id)">{{ $t('delete') }}</el-button>
          </template>
        </el-table-column> -->
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
import AddOrUpdate from './circleuser-add-or-update'
import RenPopover from "../../../components/ren-popover/ren-popover.vue"
export default {
  mixins: [mixinViewModule],
  data () {
    return {
      mixinViewModuleOptions: {
        getDataListURL: '/circleuser/circleuser/page',
        getDataListIsPage: true,
        exportURL: '/circleuser/circleuser/export',
        deleteURL: '/circleuser/circleuser',
        deleteIsBatch: true
      },
      dataForm: {
        id: '',
        circleId: '',
        userId: ''
      }
    }
  },
  components: {
    AddOrUpdate,
    RenPopover
  }
}
</script>
