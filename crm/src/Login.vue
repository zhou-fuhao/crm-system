<template>
  <div class="container">
    <main class="mainBox">
      <h1 class="title">CRM客户关系管理系统</h1>
      <p class="tip">为保护安全，请妥善保护密码</p>
      <section class="loginBox">
        <div class="form">
          <i class="iconfont icon-yonghuming"></i>
          <input type="text" class="userName" v-model="account" placeholder="请输入用户名/手机号/邮箱">
        </div>
        <div class="form">
          <i class="iconfont icon-mima"></i>
          <input type="password" class="userPass" v-model="password" placeholder="请输入密码">
        </div>
        <button class="submit" @click="submit">登录</button>
      </section>
    </main>
    <footer class="footerBox">
      <span>京网备案</span>
      <span>京网备案</span>
      <span>京网备案</span>
    </footer>
  </div>
</template>

<script>
import md5 from "blueimp-md5";
import { handleLogin } from "./api/login";
export default {
  data() {
    return {
      account: "",
      password: "",
    };
  },
  methods: {
    // 校验用户名/手机号/邮箱
    checkAccount() {
      let arrReg = [
        /^[\u4E00-\u9FA5]{1,}$/,
        /^1\d{10}$/,
        /^[a-z0-9A-Z]+[- | a-z0-9A-Z . _]+@([a-z0-9A-Z]+(-[a-z0-9A-Z]+)?\\.)+[a-z]{2,}$/,
      ];
      // some只要有一次的返回结果是true，那some的整体返回结果就是true
      return arrReg.some((item) => {
        return item.test(this.account);
      });
    },
    // 校验密码
    checkPassword() {
      let pwdReg = /^\w{6,16}$/;
      return pwdReg.test(this.password);
    },
    // 登录提交
    submit() {
      // 表单验证
      if (!this.checkAccount() || !this.checkPassword()) {
        this.$message.error("您输入的用户名或密码不符合规则！");
        return;
      }

      // 正常的登录逻辑
      handleLogin({
        account: this.account,
        password: md5(this.password),
      }).then((result) => {
          this.$alert("恭喜您，登录成功！", {
            callback: (action) => {
              this.$message({
                type: "info",
                message: `action: ${action}`,
              });
              // location.origin    "http://127.0.0.1:8080"
              location.href = location.origin;
            },
          });
        }).catch((reason) => {
          this.$alert("用户名或密码不正确！");
        });
    },
  },
};
</script>

<style lang='less' scope>
html,
body,
.container {
  height: 100%;
  font-size: 14px;
  overflow: hidden;
}

.container {
  overflow: hidden;
  height: 100%;
  font-size: 14px;
  background: url("./assets/login_bg.jpg") repeat-x;
  background-size: 100% 50%;
}

.mainBox {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -60%);
  width: 400px;

  .title,
  .tip {
    font-size: 30px;
    line-height: 50px;
    text-align: center;
    color: #fff;
    font-weight: normal;
  }

  .tip {
    font-size: 18px;
    color: #bebebf;
  }

  .loginBox {
    box-sizing: border-box;
    margin-top: 20px;
    padding: 25px;
    background: #fff;
    border: 1px solid #f4f4f4;
    box-shadow: 0px 5px 5px rgba(0, 0, 0, 0.3);

    .form {
      position: relative;
      margin: 10px 0;

      input {
        box-sizing: border-box;
        padding-left: 40px;
        width: 100%;
        height: 50px;
        line-height: 50px;
        border: 1px solid #aaa;
      }

      i {
        position: absolute;
        left: 10px;
        top: 50%;
        transform: translateY(-50%);
        font-size: 20px;
        &.icon-mima {
          left: 7px;
          font-size: 26px;
        }
      }
    }

    .submit {
      box-sizing: border-box;
      margin: 10px 0;
      width: 100%;
      height: 48px;
      line-height: 48px;
      text-align: center;
      font-size: 18px;
      color: #fff;
      background: #2299ee;
      border: none;
      border-radius: 2px;
      cursor: pointer;
    }
  }
}

.footerBox {
  position: absolute;
  bottom: 10px;
  left: 0;
  width: 100%;
  line-height: 50px;
  text-align: center;
  color: #999;

  span {
    padding: 0 10px;
  }
}
</style>
