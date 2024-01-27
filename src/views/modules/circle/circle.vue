<template>
  <el-card shadow="never" class="aui-card--fill">
    <div class="mod-circle__circle}">
      <el-form :inline="true" :model="dataForm" @keyup.enter.native="getDataList()">
        <el-form-item>
          <el-input v-model="dataForm.id" placeholder="圈子id" clearable></el-input>
        </el-form-item>
        <el-form-item>
          <el-input v-model="dataForm.circleName" placeholder="圈子名称" clearable></el-input>
        </el-form-item>
        <el-form-item>
          <el-input v-model="dataForm.userId" placeholder="圈主ID" clearable></el-input>
        </el-form-item>
        <el-form-item>
          <el-select v-model="dataForm.circleClassId" clearable filterable placeholder="圈子分类">
            <el-option
              v-for="item in circleList"
              :key="item.id"
              :label="item.name"
              :value="item.id">
            </el-option>
          </el-select>
        </el-form-item>
        <el-form-item>
          <el-button @click="getDataList()">{{ $t('query') }}</el-button>
        </el-form-item>
        <el-form-item>
          <el-button type="info" @click="exportHandle()">{{ $t('export') }}</el-button>
        </el-form-item>
        <el-form-item>
          <el-button v-if="$hasPermission('circle:circle:save')" type="primary" @click="addOrUpdateHandle()">{{ $t('add') }}</el-button>
        </el-form-item>
        <el-form-item>
          <el-button v-if="$hasPermission('circle:circle:delete')" type="danger" @click="deleteHandle()">{{ $t('deleteBatch') }}</el-button>
        </el-form-item>
      </el-form>
      <el-table v-loading="dataListLoading" :data="dataList" border @selection-change="dataListSelectionChangeHandle" style="width: 100%;">
        <el-table-column type="selection" header-align="center" align="center" width="50"></el-table-column>
        <el-table-column prop="id" label="圈子ID" header-align="center" align="center"></el-table-column>
        <el-table-column prop="avatar" label="圈主" header-align="center" align="center">
          <template slot-scope="scope" class="qz">
            <ren-popover :id="scope.row.userId" :nickName="scope.row.nickName" :avatar="scope.row.avatar" ></ren-popover>
          </template>
        </el-table-column>
        <el-table-column prop="circleName" label="圈子名字" header-align="center" align="center"></el-table-column>
        <el-table-column prop="circleClassName" label="圈子分类" header-align="center" align="center">
          <template slot-scope="scope">
            <el-tag  type="success">{{ scope.row.circleClassName }}</el-tag>
          </template>
         
        </el-table-column>
        <el-table-column prop="circleIntroduce" label="圈子介绍" header-align="center" align="center"></el-table-column>
        <el-table-column prop="circleSculpture" label="圈子头像" header-align="center" align="center">
          <template slot-scope="scope">
            <img :src="scope.row.circleSculpture" width="60" height="60" alt="">
          </template>
        </el-table-column>
        <el-table-column prop="circleBackground" label="圈子背景" header-align="center" align="center">
          <template slot-scope="scope">
            <img :src="scope.row.circleBackground" width="60" height="60" alt="">
          </template>
        </el-table-column>
       
       
        <!-- <el-table-column prop="status" label="审核状态(0:待审核  1:通过  2:拒绝)" header-align="center" align="center"></el-table-column>
        <el-table-column prop="reason" label="理由(审核通过无需理由,审核拒绝需填写拒绝理由)" header-align="center" align="center"></el-table-column> -->
        <el-table-column prop="time" label="圈子创建时间" header-align="center" align="center"></el-table-column>
        <el-table-column prop="hotOk" label="是否热门圈子" header-align="center" align="center">
          <template slot-scope="scope">
            <el-tag v-if="scope.row.hotOk == 0" type="success" @click="hot(scope.row.id,1,scope.$index)">否</el-tag>
            <el-tag v-if="scope.row.hotOk == 1" type="error" @click="hot(scope.row.id,0,scope.$index)">是</el-tag>
            </template>
        </el-table-column>
        
        <!-- <el-table-column :label="$t('handle')" fixed="right" header-align="center" align="center" width="150">
          <template slot-scope="scope">
            <el-button v-if="$hasPermission('circle:circle:update')" type="text" size="small" @click="addOrUpdateHandle(scope.row.id)">{{ $t('update') }}</el-button>
            <el-button v-if="$hasPermission('circle:circle:delete')" type="text" size="small" @click="deleteHandle(scope.row.id)">{{ $t('delete') }}</el-button>
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
import RenPopover from "../../../components/ren-popover/ren-popover.vue"
export default {
  mixins: [mixinViewModule],
  data () {
    return {
      mixinViewModuleOptions: {
        getDataListURL: '/circle/circle/page',
        getDataListIsPage: true,
        exportURL: '/circle/circle/export',
        deleteURL: '/circle/circle',
        deleteIsBatch: true
      },
      circleList: [],
      dataForm: {
        id: ''
      }
    }
  },
  components: {
    RenPopover
  },
  created(){
    this.getCircleList()
  },
  methods: {
    hot(id,value,index){
      console.log(this.dataList[index].hotOk)
      this.$http.post('/circle/circle/hot',{'id':id,"hotOk":value}).then(({ data: res }) => {
         this.getDataList()
      }).catch(() => {})
    },
    getCircleList(){
      this.$http.get(`/circleclass/circleclass/circleClass`).then(({ data: res }) => {
        console.log(res.data)
         this.circleList = res.data
         console.log(this.circleList)
      }).catch(() => {})
    }

  }
}
</script>
<style>
  .qz{
    display: flex;
    flex-direction: column;
  }
</style>
