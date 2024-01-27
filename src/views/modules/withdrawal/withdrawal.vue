<template>
  <el-card shadow="never" class="aui-card--fill">
    <div class="mod-withdrawal__withdrawal}">
      <el-form :inline="true" :model="dataForm" @keyup.enter.native="getDataList()">
        <el-form-item>
          <el-input v-model="dataForm.id" placeholder="id" clearable></el-input>
        </el-form-item>
        <el-form-item>
          <el-input v-model="dataForm.userId" placeholder="用户ID" clearable></el-input>
        </el-form-item>
        <el-form-item>
          <el-input v-model="dataForm.withdrawalCode" placeholder="提现单号" clearable></el-input>
        </el-form-item>
        <el-form-item>
          <el-input v-model="dataForm.bankNo" placeholder="银行卡号" clearable></el-input>
        </el-form-item>
        <el-form-item>
          <el-input v-model="dataForm.name" placeholder="持卡人" clearable></el-input>
        </el-form-item>
        <el-form-item>
          <el-input v-model="dataForm.idno" placeholder="身份证号" clearable></el-input>
        </el-form-item>
        <el-form-item>
          <el-select v-model="dataForm.status" placeholder="审核状态">
            <el-option  label="所有" value=''></el-option>
            <el-option :key=0 label="待审核" :value=0></el-option>
            <el-option :key=1 label="通过" :value=1></el-option>
            <el-option :key=2 label="拒绝" :value=2></el-option>
          </el-select>
        </el-form-item>
        <el-form-item>
          <el-button @click="getDataList()">{{ $t('query') }}</el-button>
        </el-form-item>
        <el-form-item>
          <el-button type="info" @click="exportHandle()">{{ $t('export') }}</el-button>
        </el-form-item>
        <!-- <el-form-item>
          <el-button v-if="$hasPermission('withdrawal:withdrawal:save')" type="primary" @click="addOrUpdateHandle()">{{ $t('add') }}</el-button>
        </el-form-item> -->
        <el-form-item>
          <el-button v-if="$hasPermission('withdrawal:withdrawal:delete')" type="danger" @click="deleteHandle()">{{ $t('deleteBatch') }}</el-button>
        </el-form-item>
      </el-form>
      <el-table v-loading="dataListLoading" :data="dataList" border @selection-change="dataListSelectionChangeHandle" style="width: 100%;">
        <el-table-column type="selection" header-align="center" align="center" width="50"></el-table-column>
        <el-table-column prop="id" label="主键ID" width="200" header-align="center" align="center"></el-table-column>
        <el-table-column prop="userId" label="用户" header-align="center" align="center">
          <template slot-scope="scope" class="qz">
            <ren-popover :id="scope.row.userId" :memberNichen="scope.row.memberNichen" :avatar="scope.row.avatar" ></ren-popover>
          </template>
        </el-table-column>
        <el-table-column prop="withdrawalCode" label="提现单号" width="200" header-align="center" align="center"></el-table-column>

        <el-table-column prop="bankName" label="银行名称" width="150" header-align="center" align="center"></el-table-column>
        <el-table-column prop="bankNo" label="银行卡号" width="150" header-align="center" align="center"></el-table-column>
        <el-table-column prop="name" label="持卡人" width="150" header-align="center" align="center"></el-table-column>
        <el-table-column prop="idno" label="身份证号" width="150" header-align="center" align="center"></el-table-column>
        <el-table-column prop="withdrawalSpecies" label="提现金币(个)" width="150" header-align="center" align="center"></el-table-column>
        <el-table-column prop="actualAccount" label="实际到账(元)" width="150" header-align="center" align="center"></el-table-column>
        <el-table-column prop="serviceCharge" label="手续费(元)" width="150" header-align="center" align="center"></el-table-column>
        <el-table-column prop="serviceChargeScale" label="手续费比例(%)" width="150" header-align="center" align="center"></el-table-column>

        <el-table-column prop="frontSpecies" label="前余额" width="150" header-align="center" align="center"></el-table-column>
        <el-table-column prop="behindSpecies" label="后余额" width="150" header-align="center" align="center"></el-table-column>
       
        <el-table-column prop="status" label="审核状态"  width="100" header-align="center" align="center">
          <template slot-scope="scope">
            <el-tag v-if="scope.row.status === 0" size="warning" type="warning">待审核</el-tag>
            <el-tag v-if="scope.row.status === 1" size="success" type="success">审核通过</el-tag>
            <el-tag v-if="scope.row.status === 2" size="info" type="info">审核拒绝</el-tag>
          </template>
        </el-table-column>
        <el-table-column prop="content" label="审核内容" :show-overflow-tooltip="true" width="200" header-align="center" align="center"></el-table-column>
        <el-table-column prop="createTime" label="创建时间" width="150" header-align="center" align="center"></el-table-column>
        <el-table-column :label="$t('handle')" fixed="right" header-align="center" align="center" width="150">
          <template slot-scope="scope">
            <el-button v-if="$hasPermission('withdrawal:withdrawal:update')" type="text" size="small" @click="addOrUpdateHandle(scope.row.id)">{{ $t('examine') }}</el-button>
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
import AddOrUpdate from './withdrawal-add-or-update'
import RenPopover from "../../../components/ren-popover/ren-popover.vue"
export default {
  mixins: [mixinViewModule],
  data () {
    return {
      mixinViewModuleOptions: {
        getDataListURL: '/withdrawal/withdrawal/page',
        getDataListIsPage: true,
        exportURL: '/withdrawal/withdrawal/export',
        deleteURL: '/withdrawal/withdrawal',
        deleteIsBatch: true
      },
      evaluatePictureList:[],
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
    selectTime(){
      if(this.times){
        console.log(this.times[0])
        console.log(this.times[1])
        this.dataForm.startTime = this.times[0]
        this.dataForm.endTime = this.times[1]
      }else{
        this.dataForm.startTime = ''
        this.dataForm.endTime = ''
      }
      console.log(this.dataForm)
    }
  },
  components: {
    AddOrUpdate,
    RenPopover
  }
}
</script>
