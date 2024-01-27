<template>
      <el-popover trigger="hover" placement="right" :open-delay="300">
              <div class="elpopo">
                <div class="elpopo-left">
                  <div class="left-top">
                    <img :src="avatar" width="120" height="80" alt="">
                    <p>ID:{{UserMessage.id}}</p>
                    <P>{{UserMessage.nickName}}</P>
                    <P>生日:{{UserMessage.birth}} | 等级:LV.{{UserMessage.level}} |</P>
                    <P>签名:{{UserMessage.signature}}</P>
                  </div>
                </div>
                <div class="elpopo-right">
                  <div class="right-text">
                      <p class="tetx-p">手机号</p>
                      <p class="tetx-p">{{UserMessage.mobile}}</p>
                  </div>
                  <div class="right-text">
                      <p class="tetx-p">邮箱</p>
                      <p class="tetx-p">{{UserMessage.email}}</p>
                  </div>
                  <div class="right-text">
                      <p class="tetx-p">金币余额</p>
                      <p class="tetx-p">{{UserMessage.cion}}</p>
                  </div>
                  <div class="right-text">
                      <p class="tetx-p">可提现余额</p>
                      <p class="tetx-p">{{UserMessage.balance}}</p>
                  </div>
                 <div class="right-text">
                      <p class="tetx-p">性别</p>
                      <p class="tetx-p" v-if="UserMessage.gender == 0">男</p>
                      <p class="tetx-p" v-if="UserMessage.gender == 1">女</p>
                      <p class="tetx-p" v-if="UserMessage.gender == 2">未知</p>
                  </div>
                   <div class="right-text">
                      <p class="tetx-p">职位</p>
                      <p class="tetx-p">{{UserMessage.job}}</p>
                  </div>
                  <div class="right-text">
                      <p class="tetx-p">婚姻状态</p>
                      <p class="tetx-p">{{UserMessage.emotion}}</p>
                  </div>
                  <div class="right-text">
                      <p class="tetx-p">上级ID</p>
                      <p class="tetx-p">{{UserMessage.pid}}</p>
                  </div>
                  <!-- <div class="right-text">
                      <p class="tetx-p">系统版本</p>
                      <p class="tetx-p">{{UserMessage.phoneVersion}}</p>
                  </div>  -->
                  <div class="right-text">
                      <p class="tetx-p">登入IP</p>
                      <p class="tetx-p">{{UserMessage.loginIp}}</p>
                  </div>
                  <div class="right-text">
                      <p class="tetx-p">注册时间</p>
                      <p class="tetx-p">{{UserMessage.createTime}}</p>
                  </div>
                  <div class="right-text">
                      <p class="tetx-p">最后活跃时间</p>
                      <p class="tetx-p">{{UserMessage.lastLoginTime}}</p>
                  </div>
                  <div class="right-text">
                      <p class="tetx-p">VIP到期时间</p>
                      <p class="tetx-p">{{UserMessage.vipDate}}</p>
                  </div>
                </div>
              </div>
              <div slot="reference" class="name-wrapper">
                  <img :src="avatar" width="40" height="40" alt=""  @mouseenter="getUserID(id)" />
                <br>
                <span>{{nickName}}</span>
              </div>
            </el-popover>
</template>

<script>
    export default{
        props:{
           id: String,
           nickName: String,
           avatar: String
        },
        data(){
            return {
                // 用户信息数据
                UserMessage:{}
            }
        },
        methods:{
            // 获取用户ID val》id
            getUserID (val){
              this.$http.get(`/user/user/modelUser/`+val).then(({ data: res }) => {
                if (res.code !== 0) {
                  return this.$message.error(res.msg)
                }
                this.UserMessage = res.data
              }).catch(() => {})
            }
  }
    }
</script>

<style >
  .elpopo{
    width: 640px;
    max-height: 480px;
    display: flex;
    
  }
  p{
    margin: 0;
    padding: 4px;
  }
  .elpopo-left{
      width: 30%;
      display: flex;
      flex-direction: column;
    }
  .elpopo-right{
    width: 70%;
    display: flex;
    flex-direction: column;
  }
  .right-text{
    width: 100%;
    display: flex;
    justify-content: space-around;
    text-align: right;
    margin-top: 4px;   
  }
  .tetx-p{
    width: 45%;
    margin: 0;
    padding: 0;
  }
</style>