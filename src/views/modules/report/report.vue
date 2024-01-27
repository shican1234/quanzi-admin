<template>
  <el-card shadow="never" class="aui-card--fill">
    <div class="mod-report__report}">
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
          <el-button v-if="$hasPermission('report:report:save')" type="primary" @click="addOrUpdateHandle()">{{ $t('add') }}</el-button>
        </el-form-item>
        <el-form-item>
          <el-button v-if="$hasPermission('report:report:delete')" type="danger" @click="deleteHandle()">{{ $t('deleteBatch') }}</el-button>
        </el-form-item>
      </el-form>
      <el-table v-loading="dataListLoading" :data="dataList" border @selection-change="dataListSelectionChangeHandle" style="width: 100%;">
        <el-table-column type="selection" header-align="center" align="center" width="50"></el-table-column>
        <el-table-column prop="id" label="ID" header-align="center" align="center"></el-table-column>
        <el-table-column prop="fromUser" label="举报者Id" header-align="center" align="center">
          <template slot-scope="scope" class="qz">
            <ren-popover :id="scope.row.fromUser" :nickName="scope.row.fromName" :avatar="scope.row.fromAvatar" ></ren-popover>
          </template>
        </el-table-column>
        <el-table-column prop="toUser" label="被举报者ID" header-align="center" align="center">
          <template slot-scope="scope" class="qz">
            <ren-popover :id="scope.row.toUser" :nickName="scope.row.toName" :avatar="scope.row.toAvatar" ></ren-popover>
          </template>
        </el-table-column>
        <el-table-column prop="reasons" label="举报原因" header-align="center" align="center"></el-table-column>
        <el-table-column prop="createTime" label="举报时间" header-align="center" align="center"></el-table-column>
        <el-table-column prop="screenShot" label="图片" header-align="center" align="center">
          <template slot-scope="scope">
            <el-image
                  style="width: 50px; height: 50px"
                  :src="JSON.parse(scope.row.screenShot)[0]"
                  :preview-src-list="evaluatePictureList"
                  title="点击查看图片"
                  @click="clickevaluatePicture(JSON.parse(scope.row.screenShot))"
                />
           </template>
        </el-table-column>
        <el-table-column prop="postId" label="被举报的帖子ID" header-align="center" align="center"></el-table-column>
        <el-table-column prop="circleId" label="被举报的圈子ID" header-align="center" align="center"></el-table-column>
        <el-table-column prop="status" label="处理进度" header-align="center" align="center">
          <template slot-scope="scope">
            <el-tag v-if="scope.row.status == 0" >
              待处理
            </el-tag>
            <el-tag v-if="scope.row.status == 1" >
              已处理
            </el-tag>
            <el-tag v-if="scope.row.status == 2" >
              已关闭
            </el-tag>
          </template>
        </el-table-column>
        <el-table-column :label="$t('handle')" fixed="right" header-align="center" align="center" width="150">
          <template slot-scope="scope">
            <el-button v-if="scope.row.status == 0" type="text" size="small" @click="pass(scope.row.id,1)">处理</el-button>
            <el-button v-if="scope.row.status == 0" type="text" size="small" @click="pass(scope.row.id,2)">关闭</el-button>
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
import AddOrUpdate from './report-add-or-update'
export default {
  mixins: [mixinViewModule],
  data () {
    return {
      mixinViewModuleOptions: {
        getDataListURL: '/report/report/page',
        getDataListIsPage: true,
        exportURL: '/report/report/export',
        deleteURL: '/report/report',
        deleteIsBatch: true
      },
      dataForm: {
        id: ''
      }
    }
  },
  components: {
    AddOrUpdate
  },
  methods: {
   
    pass(id,status){
      this.$http.post('/report/report/pass',{'id':id,"status":status}).then(({ data: res }) => {
         this.query()
      }).catch(() => {})
    },
    clickevaluatePicture(url) {
      var srclist = []
        for (const imgArr of url) {
          // 把数据库传来的图片放进数组里
          srclist.push(imgArr) 
        }
        this.evaluatePictureList = srclist// 赋值
        console.log(this.evaluatePictureList)
      }
  }
}
</script>
