<template>
  <el-card shadow="never" class="aui-card--fill">
    <div class="mod-home">
      <div class="dis-f align-center client-width">
      </div>
      <div class="dis-f align-stretch client-width margint30">
        <div class="flex1 marginr20 dis-f align-center home-item justify-between flex-column">
          <h2 class="fw-bold marginv10">今日充值金额</h2>
          <div class="fs-20">{{todayPaySum}}元</div>
          <div
            class="dis-f align-center justify-center margint20 marginb10"
          >
          </div>
        </div>
        <div class="flex1 marginr20 dis-f align-center home-item justify-between flex-column">
          <h2 class="fw-bold marginv10">本周充值金额</h2>
          <div class="fs-20">{{thisWekPaySum}}元</div>
          <div
            class="dis-f align-center justify-center margint20 marginb10"
          >
          </div>
        </div>
        <div class="flex1 marginr20 dis-f align-center home-item justify-between flex-column">
          <h2 class="fw-bold marginv10">本月总充值金额</h2>
          <div class="fs-20">{{thisMonPaySum}}元</div>
          <div
            class="dis-f align-center justify-center margint20 marginb10"
          >
          </div>
        </div>
       
        <div class="flex1 marginr20 dis-f align-center home-item justify-between flex-column">
          <h2 class="fw-bold marginv10">总充值金额</h2>
          <div class="fs-20">{{allPaySum}}元</div>
          <div
            class="dis-f align-center justify-center margint20 marginb10"
          >
          </div>
        </div>
      </div>
      <div class="dis-f align-stretch client-width margint20">
        <div class="flex1 marginr20 dis-f align-center home-item justify-between flex-column">
          <h2 class="fw-bold marginv10">今日新增用户</h2>
          <div class="fs-20">{{todayUser}}人</div>
          <div
            class="dis-f align-center justify-center margint20 marginb10"
          >
          </div>
        </div>
        <div class="flex1 marginr20 dis-f align-center home-item justify-between flex-column">
          <h2 class="fw-bold marginv10">本周新增用户</h2>
          <div class="fs-20">{{thisWekUser}}人</div>
          <div
            class="dis-f align-center justify-center margint20 marginb10"
          >
          </div>
        </div>
        <div class="flex1 marginr20 dis-f align-center home-item justify-between flex-column">
          <h2 class="fw-bold marginv10">本月新增用户</h2>
          <div class="fs-20">{{thisMonUser}}人</div>

          <div
            class="dis-f align-center justify-center margint20 marginb10"
          >
          </div>
        </div>
        <div class="flex1 marginr20 dis-f align-center home-item justify-between flex-column">
          <h2 class="fw-bold marginv10">总用户数</h2>
          <div class="fs-20">{{allUser}}人</div>
          <div
            class="dis-f align-center justify-center margint20 marginb10"
          >
          </div>
        </div>
      </div>
      <div class="dis-f align-stretch client-width margint20">
        <div class="flex1 marginr20 dis-f align-center home-item justify-between flex-column">
          <h2 class="fw-bold marginv10">今日新增圈子数</h2>
          <div class="fs-20">{{todayCircle}}个</div>
          <div
            class="dis-f align-center justify-center margint20 marginb10"
          >
           
          </div>
        </div>
        <div class="flex1 marginr20 dis-f align-center home-item justify-between flex-column">
          <h2 class="fw-bold marginv10">本周新增圈子数</h2>
          <div class="fs-20">{{thisWekCircle}}个</div>
          <div
            class="dis-f align-center justify-center margint20 marginb10"
          >
           
          </div>
        </div>
        <div class="flex1 marginr20 dis-f align-center home-item justify-between flex-column">
          <h2 class="fw-bold marginv10">本月新增圈子数</h2>
          <div class="fs-20">{{thisMonCircle}}个</div>
          <div
            class="dis-f align-center justify-center margint20 marginb10"
          >
           
          </div>
        </div>
        <div class="flex1 marginr20 dis-f align-center home-item justify-between flex-column">
          <h2 class="fw-bold marginv10">总圈子数</h2>
          <div class="fs-20">{{allCircle}}个</div>
          <div
            class="dis-f align-center justify-center margint20 marginb10"
          >
           
          </div>
        </div>
      </div>
      <div class="dis-f align-stretch client-width margint20">
        <div class="flex1 marginr20 dis-f align-center home-item justify-between flex-column">
          <h2 class="fw-bold marginv10">今日新增帖子数</h2>
          <div class="fs-20">{{todayPost}}条</div>
          <div
            class="dis-f align-center justify-center margint20 marginb10"
          >
           
          </div>
        </div>
        <div class="flex1 marginr20 dis-f align-center home-item justify-between flex-column">
          <h2 class="fw-bold marginv10">本周新增帖子数</h2>
          <div class="fs-20">{{thisWekPost}}条</div>
          <div
            class="dis-f align-center justify-center margint20 marginb10"
          >
           
          </div>
        </div>
        <div class="flex1 marginr20 dis-f align-center home-item justify-between flex-column">
          <h2 class="fw-bold marginv10">本月新增帖子数</h2>
          <div class="fs-20">{{thisMonPost}}条</div>
          <div
            class="dis-f align-center justify-center margint20 marginb10"
          >
           
          </div>
        </div>
        <div class="flex1 marginr20 dis-f align-center home-item justify-between flex-column">
          <h2 class="fw-bold marginv10">总帖子数</h2>
          <div class="fs-20">{{allPost}}条</div>
          <div
            class="dis-f align-center justify-center margint20 marginb10"
          >
           
          </div>
        </div>
      </div>
    </div>
  </el-card>
</template>
<style>
.mod-home {
  line-height: 1.5;
}
.client-width {
  width: 100%;
}
.dis-f {
  display: flex;
}
.flex1 {
  flex: 1;
}
.justify-center {
  justify-content: center;
}
.align-center {
  align-items: center;
}
.align-stretch {
  align-items: stretch;
}
.marginr20 {
  margin-right: 20px;
}
.margint30 {
  margin-top: 30px;
}
.margint20 {
  margin-top: 20px;
}
.home-item {
  box-shadow: 0 2px 12px 0 rgba(0,0,0,.1);
  padding: 20px 30px;
}
.justify-between {
  justify-content: space-between;
}
.flex-column {
  flex-direction: column;
}
.marginb10 {
  margin-bottom: 10px;
}
.marginv10 {
  margin-top: 10px;
  margin-bottom: 10px;
}
.fw-bold {
  font-weight: bold;
}
.fs-14 {
  font-size: 14px;
}
.c-dark {
  color: #666;
}
.paddingt5 {
  padding-top: 5px;
}
.paddingt10 {
  padding-top: 10px;
}
.fs-20 {
  font-size: 20px;
}
.paddingv5 {
  padding-top: 5px;
  padding-bottom: 5px;
}
</style>
<script>
export default {
  data () {
    return {
      todayUser: '0',
      thisWekUser: '0',
      thisMonUser: '0',
      allUser: '0',

      todayPaySum: '0',
      thisWekPaySum: '0',
      thisMonPaySum: '0',
      allPaySum: '0',

      todayCircle: '0',
      thisWekCircle: '0',
      thisMonCircle: '0',
      allCircle: '0',

      todayPost: '0',
      thisWekPost: '0',
      thisMonPost: '0',
      allPost: '0'
    }
  },
  created(){
    this.getInfo()
  },
  methods: {
    // 获取信息
    getInfo () {
      this.$http.get(`/sys/home/queryHome`).then(({ data: res }) => {
        if (res.code !== 0) {
          return this.$message.error(res.msg)
        }
        this.todayUser = res.data.user.todayUser
        this.thisWekUser = res.data.user.thisWekUser
        this.thisMonUser = res.data.user.thisMonUser
        this.allUser = res.data.user.allUser


        this.todayPaySum = res.data.pay.todayPaySum
        this.thisWekPaySum = res.data.pay.thisWekPaySum
        this.thisMonPaySum = res.data.pay.thisMonPaySum
        this.allPaySum = res.data.pay.allPaySum

        this.todayCircle = res.data.circle.todayCircle
        this.thisWekCircle = res.data.circle.thisWekCircle
        this.thisMonCircle = res.data.circle.thisMonCircle
        this.allCircle = res.data.circle.allCircle

        this.todayPost = res.data.post.todayPost
        this.thisWekPost = res.data.post.thisWekPost
        this.thisMonPost = res.data.post.thisMonPost
        this.allPost = res.data.post.allPost
      }).catch(() => {})
    },
    hadleClick() {
      // this.$router.push({name: "detail"})
      this.$router.push({name: "home-agent"})
    },
    agentInvAgentList(){
      this.$router.push({name: "home-agentInvAgentList"})
    },
    agentSonPayAmoutList(){
      this.$router.push({name: "home-agentSonPayAmoutList"})
    },
    agentInvUserList(){
      this.$router.push({name: "home-agentInvUserList"})
    },
    todayPayUserList(){
      this.$router.push({name: "home-todayPayUserList"})
    },
    todayRegUserList(){
      this.$router.push({name: "home-todayRegUserList"})
    }
  }
}
</script>