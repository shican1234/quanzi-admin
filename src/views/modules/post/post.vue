<template>
  <el-card shadow="never" class="aui-card--fill">
    <div class="mod-post__post}">
      <el-form :inline="true" :model="dataForm" @keyup.enter.native="getDataList()">
        <el-form-item>
          <el-input v-model="dataForm.id" placeholder="帖子ID" clearable></el-input>
        </el-form-item>
        <el-form-item>
          <el-input v-model="dataForm.userId" placeholder="用户ID" clearable></el-input>
        </el-form-item>
        <el-form-item>
          <el-select v-model="dataForm.status" clearable filterable placeholder="审核状态">
            <el-option
              v-for="item in statusList"
              :key="item.status"
              :label="item.name"
              :value="item.status">
            </el-option>
          </el-select>
        </el-form-item>
        <el-form-item>
          <el-select v-model="dataForm.postClass" clearable filterable placeholder="帖子类型">
            <el-option
              v-for="item in postClassList"
              :key="item.postClass"
              :label="item.name"
              :value="item.postClass">
            </el-option>
          </el-select>
        </el-form-item>
        <el-form-item>
          <el-select v-model="dataForm.fileType" clearable filterable placeholder="媒体类型">
            <el-option
              v-for="item in fileTypeList"
              :key="item.fileType"
              :label="item.name"
              :value="item.fileType">
            </el-option>
          </el-select>
        </el-form-item>
        <el-form-item>
          <el-select v-model="dataForm.type" clearable filterable placeholder="展示类型">
            <el-option
              v-for="item in typeList"
              :key="item.type"
              :label="item.name"
              :value="item.type">
            </el-option>
          </el-select>
        </el-form-item>
        <el-form-item>
          <el-select v-model="dataForm.payType" clearable filterable placeholder="付费类型">
            <el-option
              v-for="item in payTypeList"
              :key="item.payType"
              :label="item.name"
              :value="item.payType">
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
          <el-button v-if="$hasPermission('post:post:save')" type="primary" @click="addOrUpdateHandle()">{{ $t('add') }}</el-button>
        </el-form-item>
        <el-form-item>
          <el-button v-if="$hasPermission('post:post:delete')" type="danger" @click="deleteHandle()">{{ $t('deleteBatch') }}</el-button>
        </el-form-item>
      </el-form>
      <el-table v-loading="dataListLoading" :data="dataList" border @selection-change="dataListSelectionChangeHandle" style="width: 100%;">
        <el-table-column type="selection" header-align="center" align="center" width="50"></el-table-column>
        <el-table-column prop="id" label="帖子ID" header-align="center" align="center"></el-table-column>
        <el-table-column prop="title" label="标题" header-align="center" align="center"></el-table-column>
        <el-table-column prop="content" label="帖子文字内容" header-align="center" align="center"></el-table-column>
        <el-table-column prop="fileUrls" label="图片/视频" header-align="center" align="center">
          <template slot-scope="scope">
            <span v-if="scope.row.fileType == 0">
                <el-image
                  style="width: 50px; height: 50px"
                  :src="scope.row.image"
                  :preview-src-list="evaluatePictureList"
                  title="点击查看图片"
                  @click="clickevaluatePicture(JSON.parse(scope.row.fileUrls))"
                />
              </span>
              <span v-else-if="scope.row.fileType == 1">
                <img 
                :src="scope.row.image"
                style="width: 50px; height: 50px"
                @click="playVideo(JSON.parse(scope.row.fileUrls)[0])"
                title="点击播放" 
                alt="点击播放"/>
              </span>
              <span v-else>无</span>
           </template>
        </el-table-column>
        <el-table-column prop="userId" label="发帖的用户" header-align="center" align="center">
          <template slot-scope="scope" class="qz">
            <ren-popover :id="scope.row.userId" :nickName="scope.row.nickName" :avatar="scope.row.avatar" ></ren-popover>
          </template>
        </el-table-column>
        <el-table-column prop="circleName" label="所属圈子" header-align="center" align="center"></el-table-column>
        <el-table-column prop="status" label="状态" header-align="center" align="center">
          <template slot-scope="scope">
            <el-tag v-if="scope.row.status == 0" type="success">待审核</el-tag>
            <el-tag v-if="scope.row.status == 1" type="success">审核通过</el-tag>
            <el-tag v-if="scope.row.status == 2" type="error">拒绝</el-tag>
            </template>
        </el-table-column>
        <el-table-column prop="type" label="类型" header-align="center" align="center">
          <template slot-scope="scope">
            <el-tag v-if="scope.row.type == 0" type="success">公开帖子</el-tag>
            <el-tag v-if="scope.row.type == 1" type="error">圈内私密帖子</el-tag>
            </template>
        </el-table-column>
        <el-table-column prop="createTime" label="发帖时间" header-align="center" align="center"></el-table-column>
        <el-table-column prop="fileType" label="文件类型" header-align="center" align="center">
          <template slot-scope="scope">
            <el-tag v-if="scope.row.fileType == 0" type="success">图片</el-tag>
            <el-tag v-else-if="scope.row.fileType == 1" type="success">视频</el-tag>
            <el-tag v-else type="success">其他帖子</el-tag>
            </template>
        </el-table-column>
        <el-table-column prop="payType" label="付费贴类型" header-align="center" align="center">
          <template slot-scope="scope">
            <el-tag v-if="scope.row.payType == 0" type="success">不付费</el-tag>
            <el-tag v-if="scope.row.payType == 1" type="success">文字付费</el-tag>
            <el-tag v-if="scope.row.payType == 2" type="success">视频图片付费</el-tag>
            <el-tag v-if="scope.row.payType == 3" type="success">全部付费</el-tag>
            </template>
        </el-table-column>
        <el-table-column prop="payPrice" label="付费贴价钱" header-align="center" align="center"></el-table-column>
        <el-table-column prop="postClass" label="帖子类型" header-align="center" align="center">
          <template slot-scope="scope">
            <el-tag v-if="scope.row.postClass == 0" type="success">普通贴</el-tag>
            <el-tag v-if="scope.row.postClass == 1" type="success">红包贴</el-tag>
            <el-tag v-if="scope.row.postClass == 2" type="success">投票贴</el-tag>
            <el-tag v-if="scope.row.postClass == 3" type="success">问答贴</el-tag>
            </template>
        </el-table-column>
        <!-- <el-table-column prop="discussId" label="话题ID" header-align="center" align="center"></el-table-column> -->
       
        <el-table-column :label="$t('handle')" fixed="right" header-align="center" align="center" width="150">
          <template slot-scope="scope">
            <el-button v-if="scope.row.status == 0 " type="text" size="small" @click="pass(scope.row.id,1)">通过</el-button>
            <el-button  v-if="scope.row.status == 0 " type="text" size="small" @click="pass(scope.row.id,2)">拒绝</el-button>
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
      <el-dialog
      title="视频预览"
      :visible.sync="dialogPlay"
      v-model="dialogPlay"
      width="200"
      @close="closeDialog"
      >
      <video   
        :src= "playVideoUrl"
        controls
        autoplay
        class="video"
        ref="dialogVideo"
        width="100%" 
        height="400"
      ><source type='video/mp4; codecs="avc1.42E01E, mp4a.40.2"'></video>
      </el-dialog>
    </div>
  </el-card>
</template>

<script>
import mixinViewModule from '@/mixins/view-module'
import AddOrUpdate from './post-add-or-update'
import RenPopover from "../../../components/ren-popover/ren-popover.vue"
export default {
  mixins: [mixinViewModule],
  data () {
    return {
      mixinViewModuleOptions: {
        getDataListURL: '/post/post/page',
        getDataListIsPage: true,
        exportURL: '/post/post/export',
        deleteURL: '/post/post',
        deleteIsBatch: true
      },
      statusList: [
        {"name":"全部","status":''},
        {"name":"待审核","status":0},
        {"name":"审核通过","status":1},
        {"name":"已拒绝","status":2}
      ],
      postClassList: [
        {"name":"全部","postClass":''},
        {"name":"普通贴","postClass":0},
        {"name":"红包贴","postClass":1},
        {"name":"投票贴","postClass":2},
        {"name":"问答贴","postClass":3}
      ],
      typeList: [
        {"name":"全部","type":''},
        {"name":"公开帖子","type":0},
        {"name":"圈内帖子","type":1}
      ],
      fileTypeList: [
        {"name":"全部","fileType":''},
        {"name":"图片","fileType":0},
        {"name":"视频","fileType":1}
      ],
      payTypeList: [
        {"name":"全部","payType":''},
        {"name":"不付费","payType":0},
        {"name":"文字付费","payType":1},
        {"name":"视频图片付费","payType":2},
        {"name":"全部付费","payType":3}
      ],
      playVideoUrl: "",
      dialogPlay: false,
      evaluatePictureList:[],
      dataForm: {
        id: '',
        userId: '',
        status: '',
        postClass: '',
        type: '',
        fileType: '',
        payType: ''
      }
    }
  },
  components: {
    AddOrUpdate,
    RenPopover
  },
  methods: {
    // 视频
    playVideo(url) {
      console.log(url)
      this.dialogPlay = true;
      this.playVideoUrl = url;
    },
    pass(id,status){
      this.$http.post('/post/post/pass',{'id':id,"status":status}).then(({ data: res }) => {
         this.query()
      }).catch(() => {})
    },
    closeDialog() {
      this.playVideoUrl = ""; //清空数据 关闭视频播放
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
