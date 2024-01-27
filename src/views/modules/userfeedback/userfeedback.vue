<template>
  <el-card shadow="never" class="aui-card--fill">
    <div class="mod-userfeedback__userfeedback}">
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
        <!-- <el-form-item>
          <el-button v-if="$hasPermission('userfeedback:userfeedback:save')" type="primary" @click="addOrUpdateHandle()">{{ $t('add') }}</el-button>
        </el-form-item> -->
        <el-form-item>
          <el-button v-if="$hasPermission('userfeedback:userfeedback:delete')" type="danger" @click="deleteHandle()">{{ $t('deleteBatch') }}</el-button>
        </el-form-item>
      </el-form>
      <el-table v-loading="dataListLoading" :data="dataList" border @selection-change="dataListSelectionChangeHandle" style="width: 100%;">
        <el-table-column type="selection" header-align="center" align="center" width="50"></el-table-column>
        <el-table-column prop="id" label="主键ID" width="200" header-align="center" align="center"></el-table-column>
        <el-table-column prop="userId" label="用户" header-align="center" align="center">
          <template slot-scope="scope" class="qz">
            <ren-popover :id="scope.row.userId" :memberNichen="scope.row.userName" :avatar="scope.row.avatar" ></ren-popover>
          </template>
        </el-table-column>

        <el-table-column prop="userName" label="联系名称" width="150" header-align="center" align="center"></el-table-column>
        <el-table-column prop="contactInformation" label="联系方式" width="150" header-align="center" align="center"></el-table-column>
        <el-table-column prop="feedbackContent" label="反馈内容" :show-overflow-tooltip="true" width="200" header-align="center" align="center"></el-table-column>
        <el-table-column prop="images" label="反馈图片" width="150" header-align="center" align="center">
          <template slot-scope="scope">
          <el-image style="width: 100px; height: 100px" v-if="scope.row.images != null"  :src="scope.row.images[0]" :preview-src-list="scope.row.images"></el-image>
          <el-image style="width: 100px; height: 100px" v-if="scope.row.images === null" :src="url" :preview-src-list="scope.row.images"></el-image>
        </template>
        </el-table-column>
        
        <el-table-column prop="type" label="回复状态" :show-overflow-tooltip="true" width="100" header-align="center" align="center">
          <template slot-scope="scope">
            <el-tag v-if="scope.row.type === 0" >未回复</el-tag>
            <el-tag v-if="scope.row.type === 1" type="success">已回复</el-tag>
          </template>
        </el-table-column>

        <el-table-column prop="status" label="是否解决" :show-overflow-tooltip="true" width="100" header-align="center" align="center">
          <template slot-scope="scope">
            <el-tag v-if="scope.row.status === 0"  type="success">已解决</el-tag>
            <el-tag v-if="scope.row.status === 1"  type="warning">未解决</el-tag>
            <el-tag v-if="scope.row.status === 2"  type="info">未知</el-tag>
          </template>
        </el-table-column>
        <el-table-column prop="replyContent" label="回复内容" :show-overflow-tooltip="true" width="200" header-align="center" align="center"></el-table-column>
        <el-table-column prop="feedbackTime" label="反馈时间" width="180" header-align="center" align="center"></el-table-column>
        <el-table-column prop="replyTime" label="回复时间" width="180" header-align="center" align="center"></el-table-column>


        <el-table-column :label="$t('handle')" fixed="right" header-align="center" align="center" width="150">
          <template slot-scope="scope">
            <el-button v-if="$hasPermission('userfeedback:userfeedback:update')" type="text" size="small" @click="addOrUpdateHandle(scope.row.id)">{{ $t('update') }}</el-button>
            <el-button v-if="$hasPermission('userfeedback:userfeedback:delete')" type="text" size="small" @click="deleteHandle(scope.row.id)">{{ $t('delete') }}</el-button>
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
import AddOrUpdate from './userfeedback-add-or-update'
import RenPopover from "../../../components/ren-popover/ren-popover.vue"
export default {
  mixins: [mixinViewModule],
  data () {
    return {
      mixinViewModuleOptions: {
        getDataListURL: '/userfeedback/userfeedback/page',
        getDataListIsPage: true,
        exportURL: '/userfeedback/userfeedback/export',
        deleteURL: '/userfeedback/userfeedback',
        deleteIsBatch: true,
        
      },
      url: 'https://fuss10.elemecdn.com/e/5d/4a731a90594a4af544c0c25941171jpeg.jpeg',
      dataForm: {
        id: ''
      }
    }
  },

  methods: {
    clickevaluatePicture(url) {
      var srclist = []
      srclist.push(url) 
      this.evaluatePictureList = srclist// 赋值
    },
  },
  components: {
    AddOrUpdate,
    RenPopover
  }
}
</script>
