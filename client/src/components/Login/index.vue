<template>
  <div class="login_body">
    <div>
      <input v-model="username" class="login_text" type="text" placeholder="账户名/手机号/Email" />
    </div>
    <div>
      <input v-model="password" class="login_text" type="password" placeholder="请输入您的密码" />
    </div>
    <div>
     <input type="text" class="login_text" placeholder="验证码" v-model="verifyImg"> <img @touchstart="handleToVerifyImg" src="/api2/users/verifyImg" alt="">
    </div>
    <div class="login_btn">
      <input type="submit" value="登录" @touchstart="handleToLogin"/>
    </div>
    <div class="login_link">
      <router-link to="/mine/register">立即注册</router-link>
      <router-link to="/mine/findPassword">找回密码</router-link>
    </div>
  </div>
</template>
<script>
import {messageBox} from '@/components/JS'
export default {
  name: "Login",
  data() {
    return {
      username:'',
      password:'',
      verifyImg:''
    }
  },
  methods:{
    handleToLogin(){
      this.axios.post('/api2/users/login',{
        username:this.username,
        password:this.password,
        verifyImg:this.verifyImg
      }).then(res=>{
          let status = res.data.status;
          let that = this;
          if(status === 0){
              messageBox({
                title:'提示',
                content:'登录成功!',
                ok:'确定',
                handleOk(){
                  that.$router.push('/mine/center')
                }
              })
          }else{
             messageBox({
                title:'提示',
                content:res.data.msg,
                ok:'确定'
              })
          }
          
      })
    },
    handleToVerifyImg(e){
      e.target.src = '/api2/users/verifyImg?'+Math.random();
    }
  }
};
</script>
<style lang="css" scoped>
#content .login_body {
  width: 100%;
}
.login_body .login_text {
  width: 100%;
  height: 40px;
  border: none;
  border-bottom: 1px #ccc solid;
  margin-bottom: 5px;
  outline: none;
  text-indent: 10px;
}
.login_body .login_btn {
  height: 50px;
  margin: 10px;
}
.login_body .login_btn input {
  width: 100%;
  height: 100%;
  background: #e54847;
  border-radius: 3px;
  border: none;
  display: block;
  color: white;
  font-size: 20px;
}
.login_body .login_link {
  display: flex;
  justify-content: space-between;
}
.login_body .login_link a {
  text-decoration: none;
  margin: 0 5px;
  font-size: 12px;
  color: #e54847;
}
</style>