<template>
  <div>
    <div>个人中心:</div>
    <div>
      当前用户:{{$store.state.user.name}}
      <a href="javascript:;" @click="handleToLogout">退出</a>
    </div>
    <div v-if="$store.state.user.isAdmin">
      用户身份:管理员
      <a href="/admin" target="_blank">管理后台</a>
    </div>
    <div v-else>用户身份:普通用户</div>
    <div>
      <input type="file" name="file" value="上传头像" @change="handleToUpload" />
      <img :src="$store.state.user.userHead" alt />
    </div>
  </div>
</template>
<script>
import axios from "axios";
import { messageBox } from "@/components/JS";
export default {
  name: "center",
  methods: {
    handleToLogout() {
      this.axios.get("/api2/users/logout").then(res => {
        let status = res.data.status;
        if (status === 0) {
          localStorage.removeItem("name");
          localStorage.removeItem("isAdmin");
          this.$store.commit("user/USER_NAME", {
            name: "",
            isAdmin: false,
            userHead: ""
          });
          this.$router.push("/mine/login");
        }
      });
    },
    handleToUpload(e) {
      let file = e.target.files[0];
      let param = new FormData();
      param.append("file", file, file.name);
      let config = {
        headers: {
          "Content-Type": "multipart/form-data"
        }
      };
      this.axios.post("/api2/users/uploadUserHead", param, config).then(res => {
        let status = res.data.status;
        let that = this;
        if (status === 0) {
          messageBox({
            title: "上传头像",
            content: "上传头像成功",
            ok: "确定",
            handleOk() {
              that.$store.commit("user/USER_NAME", {
                name: that.$store.state.user.name,
                isAdmin: that.$store.state.user.isAdmin,
                userHead: res.data.data.userHead+'?'+Math.random()
              });
            }
          });
        } else {
          messageBox({
            title: "上传头像",
            content: "上传头像失败",
            ok: "确定"
          });
        }
      });
    }
  },
  beforeRouteEnter(to, from, next) {
    axios.get("/api2/users/getUser").then(res => {
      let status = res.data.status;
      if (status === 0) {
        localStorage.setItem("name", res.data.data.username);
        localStorage.setItem("isAdmin", res.data.data.isAdmin);
        next(vm => {
          vm.$store.commit("user/USER_NAME", {
            name: res.data.data.username,
            isAdmin: res.data.data.isAdmin,
            userHead: res.data.data.userHead
          });
        });
      } else {
        next("/mine/login");
      }
    });
  }
};
</script>
<style lang="css" scoped>
</style>