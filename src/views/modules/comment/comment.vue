<template>
  <el-card shadow="never" class="aui-card--fill">
    <div class="mod-comment__comment}">
      <el-form :inline="true" :model="dataForm" @keyup.enter.native="getDataList()">
        <el-form-item>
          <el-input v-model="dataForm.commentUserId" placeholder="用户ID" clearable></el-input>
        </el-form-item>
        <el-form-item>
          <el-button @click="getDataList()">{{ $t('query') }}</el-button>
        </el-form-item>
        <el-form-item>
          <el-button type="info" @click="exportHandle()">{{ $t('export') }}</el-button>
        </el-form-item>
        <el-form-item>
          <el-button v-if="$hasPermission('comment:comment:save')" type="primary" @click="addOrUpdateHandle()">{{ $t('add') }}</el-button>
        </el-form-item>
        <el-form-item>
          <el-button v-if="$hasPermission('comment:comment:delete')" type="danger" @click="deleteHandle()">{{ $t('deleteBatch') }}</el-button>
        </el-form-item>
      </el-form>
      <el-table v-loading="dataListLoading" :data="dataList" border @selection-change="dataListSelectionChangeHandle" style="width: 100%;">
        <el-table-column type="selection" header-align="center" align="center" width="50"></el-table-column>
        <el-table-column prop="id" label="主键ID" header-align="center" align="center"></el-table-column>
        <el-table-column prop="tid" label="帖子" header-align="center" align="center"></el-table-column>
        <el-table-column prop="commentUserId" label="评论人" header-align="center" align="center">
          <template slot-scope="scope" class="qz">
            <ren-popover :id="scope.row.commentUserId" :nickName="scope.row.nickName" :avatar="scope.row.avatar" ></ren-popover>
          </template>
        </el-table-column>
        <el-table-column prop="parentId" label="所属评论id，主评论为null" header-align="center" align="center"></el-table-column>
        <el-table-column prop="content" label="评论内容" header-align="center" align="center"></el-table-column>
        <!-- <el-table-column prop="like" label="点赞(存储点赞人id数组)" header-align="center" align="center"></el-table-column> -->
        <!-- <el-table-column prop="status" label="状态，0-未审核，1-展现，2-审核驳回，3-已删除" header-align="center" align="center"></el-table-column> -->
        <el-table-column prop="createTime" label="评论时间" header-align="center" align="center"></el-table-column>
        <!-- <el-table-column prop="updateTime" label="" header-align="center" align="center"></el-table-column> -->
        <el-table-column :label="$t('handle')" fixed="right" header-align="center" align="center" width="150">
          <template slot-scope="scope">
            <el-button v-if="$hasPermission('comment:comment:delete')" type="text" size="small" @click="deleteHandle(scope.row.id)">{{ $t('delete') }}</el-button>
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
import AddOrUpdate from './comment-add-or-update'
import RenPopover from "../../../components/ren-popover/ren-popover.vue"
export default {
  mixins: [mixinViewModule],
  data () {
    return {
      mixinViewModuleOptions: {
        getDataListURL: '/comment/comment/page',
        getDataListIsPage: true,
        exportURL: '/comment/comment/export',
        deleteURL: '/comment/comment',
        deleteIsBatch: true
      },
      dataForm: {
        commentUserId: ''
      }
    }
  },
  components: {
    AddOrUpdate,
    RenPopover
  }
}
</script>
