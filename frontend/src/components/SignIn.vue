<template>
  <div class="container">
    <div class="alert alert-success alert-dismissible" role="alert" v-if="warningBox.type == 1">
      <button type="button" class="close" data-dismiss="alert" aria-label="Close" @click="closeWarningBox()"><span
          aria-hidden="true">&times;</span></button>
      <strong>太棒了！</strong> {{ warningBox.text }}
    </div>
    <div class="alert alert-danger" role="alert" v-if="warningBox.type == 2">
      <div class="alert alert-danger alert-dismissible" role="alert">
        <button type="button" class="close" data-dismiss="alert" aria-label="Close" @click="closeWarningBox()"><span
            aria-hidden="true">&times;</span></button>
        <strong>真不巧！</strong> {{ warningBox.text }}
      </div>
    </div>
    <div class="container" id="container">
      <div class="form-container sign-up-container">
        <form action="#">
          <h1>Create Account</h1>
          <div class="social-container">
            <a href="#" class="social"><i class="fab fa-facebook-f"></i></a>
            <a href="#" class="social"><i class="fab fa-google-plus-g"></i></a>
            <a href="#" class="social"><i class="fab fa-linkedin-in"></i></a>
          </div>
          <span>or use your email for registration</span>
          <input type="text" placeholder="UserName" v-model="newUser_info.uname"/>
          <input type="email" placeholder="Email"/>
          <input type="password" placeholder="Password" v-model="newUser_info.upw"/>
          <button @click="signUpConfirm()">Sign Up</button>
        </form>
      </div>
      <div class="form-container sign-in-container">
        <form action="#">
          <h1>Sign in</h1>
          <div class="social-container">
            <a href="#" class="social"><i class="fab fa-facebook-f"></i></a>
            <a href="#" class="social"><i class="fab fa-google-plus-g"></i></a>
            <a href="#" class="social"><i class="fab fa-linkedin-in"></i></a>
          </div>
          <span>or use your account</span>
          <input type="userid" placeholder="UserID" v-model="logUser_info.uno"/>
          <input type="password" placeholder="Password" v-model="logUser_info.upw"/>
          <a href="/initial">Forgot your password?</a>

          <button @click="signInConfirm()">Sign In</button>
        </form>
      </div>
      <div class="overlay-container">
        <div class="overlay">
          <div class="overlay-panel overlay-left">
            <h1>Welcome Back!</h1>
            <p>To keep connected with us please login with your personal info</p>
            <button class="ghost" id="signIn">Sign In</button>
          </div>
          <div class="overlay-panel overlay-right">
            <h1>Hello, Friend!</h1>
            <p>Enter your personal details and start journey with us</p>
            <button class="ghost" id="signUp">Sign Up</button>
          </div>
        </div>
      </div>
    </div>

    <div class="footer">
      <b> Follow me on </b>
      <div class="icons">
        <a href="#" target="_blank" class="social"><i class="fab fa-github"></i></a>
        <a href="#" target="_blank" class="social"><i class="fab fa-instagram"></i></a>
        <a href="#" target="_blank" class="social"><i class="fab fa-medium"></i></a>
        <a href="#" target="_blank" class="social"><i class="fab fa-twitter-square"></i></a>
        <a href="#" target="_blank" class="social"><i class="fab fa-linkedin"></i></a>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios'
import router from '@/router/index'
//import global from "@/components/global";

export default {
  name: 'SignIn',
  data() {
    return {
      register_url: "http://127.0.0.1:8000/api/user/signup/",
      logUser_url: "http://127.0.0.1:8000/api/user/login/",
      logUser_blank: {uno: '', upw: ''},
      newUser_blank: {uname: '', upw: ''},
      newUser_info: {uname: '', upw: ''},
      logUser_info: {uno: '', upw: ''},
      warningBox: {type: 0, text: ''}
    }
  },
  methods: {
    initLyb() {
      console.log("========> successfully create! <========");
    },

    signInConfirm() {
      var params = new URLSearchParams();
      params.append("uno", this.logUser_info.uno);
      params.append("upw", this.logUser_info.upw);
      console.log("+++++>> here is login <<+++++");

      this.$store.commit('setLoginUser', this.logUser_info);
      
      console.log(this.$store.state.loginUser);
      router.push('/initial');

      axios({
        method: 'post',
        url: this.logUser_url,
        data: params
      }).then((res) => {
        console.log("post success");

        //this.clearEditBar();
        console.log("=====> signIn SUCCESS !!! <=====");
        console.log(res.data);

        if (res.data.user_found && res.data.pw_right) {
          this.warningBox.type = 1;
          this.warningBox.text = "登陆成功";
          this.global.uno = this.logUser_info.uno;
          this.global.uname = res.data.uname;
          console.log(this.logUser_info.uno);
          console.log(this.global.uno);
          console.log(this.global.uname);
        } else if (res.data.user_found == false) {
          this.warningBox.type = 2;
          this.warningBox.text = "ID未找到，请先注册";
        } else if (res.data.pw_right == false) {
          this.warningBox.type = 2;
          this.warningBox.text = "密码错误";
        } else {
          this.warningBox.type = 2;
          this.warningBox.text = "未知错误";
        }
        this.logUser_info = this.logUser_blank;

      }).catch(err => {
        console.log(err);
      });

    },

    signUpConfirm() {

      var params = new URLSearchParams();
      params.append("uname", this.newUser_info.uname);
      params.append("upw", this.newUser_info.upw);
      console.log("+++++>> here is signUp <<+++++");
      console.log(this.newUser_info);


      axios({
        method: 'post',
        url: this.register_url,
        data: params
      }).then((res) => {
        this.newUser_info = this.newUser_blank;
        console.log("post success");

        //this.clearEditBar();
        console.log("=====> signUp SUCCESS !!! <=====");
        console.log(res.data);

        if (res.data.success) {
          this.warningBox.type = 1;
          this.warningBox.text = "注册成功，您的登录ID为：" + res.data.uno + " 请牢记";
        } else {
          this.warningBox.type = 2;
          this.warningBox.text = "注册失败，用户名或密码格式错误";
        }


      }).catch(err => {
        console.log(err);
      });

      //console.log("this is sign in ")
    },

    closeWarningBox() {
      this.warningBox.type = 0;
    }
  },

  created() {
    this.initLyb();
  }
}
</script>

<style>
@import url('https://fonts.googleapis.com/css?family=Montserrat:400,800');

* {
  box-sizing: border-box;
}

body {
  font-family: 'Montserrat', sans-serif;
  background: #f6f5f7;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  height: 100vh;
  margin: -20px 0 50px;
  margin-top: 20px;
}

h1 {
  font-weight: bold;
  margin: 0;
}

p {
  font-size: 14px;
  font-weight: 100;
  line-height: 20px;
  letter-spacing: .5px;
  margin: 20px 0 30px;
}

span {
  font-size: 12px;
}

a {
  color: #333;
  font-size: 14px;
  text-decoration: none;
  margin: 15px 0;
}

.container {
  background: #fff;
  border-radius: 10px;
  box-shadow: 0 14px 28px rgba(0, 0, 0, .2), 0 10px 10px rgba(0, 0, 0, .2);
  position: relative;
  overflow: hidden;
  width: 768px;
  max-width: 100%;
  min-height: 480px;
}

.form-container form {
  background: #fff;
  display: flex;
  flex-direction: column;
  padding: 0 50px;
  height: 100%;
  justify-content: center;
  align-items: center;
  text-align: center;
}

.social-container {
  margin: 20px 0;
}

.social-container a {
  border: 1px solid #ddd;
  border-radius: 50%;
  display: inline-flex;
  justify-content: center;
  align-items: center;
  margin: 0 5px;
  height: 40px;
  width: 40px;
}

.form-container input {
  background: #eee;
  border: none;
  padding: 12px 15px;
  margin: 8px 0;
  width: 100%;
}

button {
  border-radius: 20px;
  border: 1px solid #666666;
  background: #666666;
  color: #fff;
  font-size: 12px;
  font-weight: bold;
  padding: 12px 45px;
  letter-spacing: 1px;
  text-transform: uppercase;
  transition: transform 80ms ease-in;
}

button:active {
  transform: scale(.95);
}

button:focus {
  outline: none;
}

button.ghost {
  background: transparent;
  border-color: #fff;
}

.form-container {
  position: absolute;
  top: 0;
  height: 100%;
  transition: all .6s ease-in-out;
}

.sign-in-container {
  left: 0;
  width: 50%;
  z-index: 2;
}

.sign-up-container {
  left: 0;
  width: 50%;
  z-index: 1;
  opacity: 0;
}

.overlay-container {
  position: absolute;
  top: 0;
  left: 50%;
  width: 50%;
  height: 100%;
  overflow: hidden;
  transition: transform .6s ease-in-out;
  z-index: 100;
}

.overlay {
  background: #666666;
  background: linear-gradient(to right, #666666, #666666) no-repeat 0 0 / cover;
  color: #fff;
  position: relative;
  left: -100%;
  height: 100%;
  width: 200%;
  transform: translateY(0);
  transition: transform .6s ease-in-out;
}

.overlay-panel {
  position: absolute;
  top: 0;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  padding: 0 40px;
  height: 100%;
  width: 50%;
  text-align: center;
  transform: translateY(0);
  transition: transform .6s ease-in-out;
}

.overlay-right {
  right: 0;
  transform: translateY(0);
}

.overlay-left {
  transform: translateY(-20%);
}

/* Move signin to right */
.container.right-panel-active .sign-in-container {
  transform: translateY(100%);
}

/* Move overlay to left */
.container.right-panel-active .overlay-container {
  transform: translateX(-100%);
}

/* Bring signup over signin */
.container.right-panel-active .sign-up-container {
  transform: translateX(100%);
  opacity: 1;
  z-index: 5;
}

/* Move overlay back to right */
.container.right-panel-active .overlay {
  transform: translateX(50%);
}

/* Bring back the text to center */
.container.right-panel-active .overlay-left {
  transform: translateY(0);
}

/* Same effect for right */
.container.right-panel-active .overlay-right {
  transform: translateY(20%);
}

.footer {
  margin-top: 25px;
  text-align: center;
}

.icons {
  display: flex;
  width: 30px;
  height: 30px;
  letter-spacing: 15px;
  align-items: center;
}
</style>