<template>
  <div class="retrieve-password">
    <div class="retrieve-container">
      <!-- 进度条 -->
      <div class="progress">
        <!-- 高亮圆圈 -->
        <div class="progress-circle">1</div>
        <!-- 高亮线条 -->
        <div class="light-line"></div>
        <!-- 正在修改的页面 -->
        <template v-if="showSuccess == 'show'">
          <span class="progress-arrow">>></span>
          <div class="gray-line"></div>
          <div class="gray-circle">2</div>
        </template>
        <!-- 已经完成修改了 -->
        <template v-else>
          <span class="progress-arrow lighe-arrow">>></span>
          <div class="light-line"></div>
          <div class="progress-circle success-circle">2</div>
        </template>
        <template v-if="showSuccess != 'succ'">
          <div class="gray-line"></div>
          <span class="progress-arrow">>></span>
          <div class="gray-line"></div>
          <div class="gray-circle">3</div>
        </template>
        <template v-else>
          <div class="light-line"></div>
          <span class="progress-arrow lighe-arrow">>></span>
          <div class="light-line"></div>
          <div class="progress-circle success-circle">3</div>
        </template>

        <p class="progress-tips">
          <span class="light-tips-item" style="margin-left:13px">验证身份</span>
          <span
            class="gray-tips-item"
            style="margin-right: 4px;"
            v-if="showSuccess != 'succ'"
          >设置设置成功</span>
          <span class="gray-tips-item success-tips" style="margin-right: 4px;" v-else>设置设置成功</span>
          <span
            class="gray-tips-item"
            style="margin-right: 258px;"
            v-if="showSuccess == 'show'"
          >设置新密码</span>
          <span class="gray-tips-item success-tips" style="margin-right: 258px;" v-else>设置新密码</span>
        </p>
      </div>
      <div class="retrieve-input" v-if="showSuccess == 'show'">
        <el-alert
          v-if="messageTips"
          :closable="false"
          class="message-tips"
          :title="messageTitle"
          type="success"
          show-icon
        ></el-alert>
        <div class="form">
          <div class="item">
            <label for="phone">手机号码</label>
            <input
              v-model="userPhone"
              @blur="animateWidth('userPhone','blur')"
              class="item-inp"
              type="text"
              id="phone"
              placeholder="请输入您绑定的手机"
            />
            <span class="warning-tips" v-if="errPhoneInfoShow">
              <span class="warning-icon">!</span>
              <span class="warning-info">你输入的手机号格式不对</span>
            </span>
            <span class="warning-tips" v-if="errPhoneInfotip">
              <span class="warning-icon">!</span>
              <span class="warning-info">手机号不能为空</span>
            </span>
          </div>
          <div class="item code">
            <label for="code">验证码</label>
            <input
              v-model="codeNumInfo"
              class="item-inp"
              type="text"
              id="code"
              placeholder="请输入验证码"
            />
            <div class="get-code" v-if="!isGetCode" @click="getCode">{{codeInfo}}</div>
            <div v-else class="get-code getting-code">重新获取({{codeNum}})</div>
            <span class="warning-tips" v-if="errPhoneInNum">
              <span class="warning-icon">!</span>
              <span class="warning-info">验证码不能为空</span>
            </span>
          </div>
          <!-- <div class="item">
                        <label for="password">设置新密码</label>
                        <input v-model="userPsd" class="item-inp" type="password" id="password" placeholder="请输入新密码">
                        <span class="warning-tips" v-if="errPsdInfoShow">
                            <span class="warning-icon">!</span>
                            <span class="warning-info">密码只能是6-16位字母、数字或符号组成</span>
                        </span>
          </div>-->
          <div class="item">
            <label></label>
            <div class="next-step" @click="submitPsdStep">下一步</div>
          </div>
        </div>
      </div>
      <div v-if="showSuccess =='pass' " class="succes_pass">
        <span class="span">用户手机号：{{userp}}</span>
        <div class="item item_1">
          <label for="password">设置新密码</label>
          <input
            v-model="userPsd"
            @blur="animateWidth('company_name','blur')"
            class="item-inp"
            type="password"
            id="password"
            placeholder="请输入新密码"
          />
          <span class="warning-tips" v-if="errPsdInfoShow">
            <span class="warning-icon">!</span>
            <span class="warning-info">密码只能是6-16位字母、数字或符号组成</span>
          </span>
          <span class="warning-tips" v-if="errPsdInfoShowempty">
            <span class="warning-icon">!</span>
            <span class="warning-info">密码不能为空</span>
          </span>
        </div>
        <div class="item item_1">
          <label for="password">确认新密码</label>

          <input
            v-model="password_tow"
            @blur="animateWidth('company_name','blur')"
            class="item-inp"
            type="password"
            id="password_top"
            placeholder="请输入新密码"
          />

          <span class="warning-tips" v-if="errPsdInfoShowq">
            <span class="warning-icon">!</span>
            <span class="warning-info">确认新密码要与新密码相同，请重新填写</span>
          </span>
          <span class="warning-tips" v-if="errPsdInfoShowemp">
            <span class="warning-icon">!</span>
            <span class="warning-info">密码不能为空</span>
          </span>
        </div>

        <div class="item">
          <label></label>
          <div class="next-step" @click="submitPsd">下一步</div>
        </div>
      </div>
      <div v-if="showSuccess =='succ' " class="success-retrieve">
        <p class="success-info">
          <img src="../assets/img/success.png" alt />
          <span>修改成功，请牢记新的登录密码</span>
        </p>
        <p class="login-again">
          <span>
            <router-link to="/loginLayout">重新登录</router-link>
          </span>
        </p>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      codeInfo: "获取验证码",
      // 是否正在获取验证码
      isGetCode: false,
      // 定时时间
      codeNum: 60,
      codeNumInfo: "",
      // 定时器
      timerCode: null,
      // 用户手机号
      userPhone: "",
      errPhoneInfotip: false,
      errPhoneInNum: false,
      errPsdInfoShowempty: false,
      errPsdInfoShowemp: false,
      // 用户密码
      userPsd: "",
      password_tow: "",
      // 错误手机号显示
      errPhoneInfoShow: false,
      // 显示短信发送成功的提示
      messageTips: false,
      //提示信息
      messageTitle: "",
      // 密码错误
      errPsdInfoShow: false,
      errPsdInfoShowq: false,
      // 显示已经修改成功
      showSuccess: "show",
      re_data: "",
      userp: "",
      // possSuccess:'pass',
    };
  },
  methods: {
    formatterPhone(tel) {
      let reg = /^(\d{3})\d{4}(\d{4})$/;
      return (
        "验证码已发送至您的绑定手机" +
        tel.replace(reg, "$1****$2") +
        "，请注意查收！"
      );
    },
    animateWidth(name, type) {
      var userPsd = this.userPsd;
      var password_tow = this.password_tow;
      if (name == "company_name") {
        if (type == "blur") {
          if (!/^[\w]{6,16}$/.test(this.userPsd)) {
            this.errPsdInfoShow = true;
            this.errPsdInfoShowq = false;
            return;
          } else {
            this.errPsdInfoShow = false;
            this.errPsdInfoShowq = false;
          }
        }
      }
      if ((name = "userPhone")) {
        if (type == "blur") {
          if (!/^1(3|4|5|6|7|8|9)\d{9}$/.test(this.userPhone)) {
            this.errPhoneInfoShow = true;
            return;
          }
        }
      }
    },
    getCode() {
      // if (!(/^1(3|4|5|6|7|8|9)\d{9}$/.test(this.userPhone))) {
      //     this.errPhoneInfoShow = true;
      //     return;
      // }
      this.isGetCode = true;
      this.messageTips = true;
      this.errPhoneInfoShow = false;
      this.messageTitle = this.formatterPhone(this.userPhone);
      //此处发送请求
      var data = { mobile: this.userPhone };
      console.log(data);
      this.$api
        .retrievepassword(data)
        .then(res => {
          console.log(res);
          this.re_data = res.data.key;        
        })
        .catch(err => {
          console.log(err);
        });
      this.timerCode = setInterval(() => {
        this.codeNum -= 1;
        if (this.codeNum <= 0) {
          this.codeNum = 60;
          this.isGetCode = false;
          this.codeInfo = "重新获取";
          clearInterval(this.timerCode);
          this.timerCode = null;
        }
      }, 1000);
    },
    // 失去焦点验证

    //验证手机号
    submitPsdStep() {
      if (this.userPhone == "") {
        this.errPhoneInfotip = true;
      } else if (this.codeNumInfo == "") {
        this.errPhoneInNum = true;
        // }else if(this.errPhoneInfoShow = ){

        // }
      } else {
        var data = {
          verification_key: this.re_data,
          mobile: this.userPhone,
          verification_code: this.codeNumInfo
        };
        this.$api
          .checkpasswordsms(data)
          .then(res => {
            console.log(res);
            this.userp = res.data.mobile;
            console.log(this.userp);
            if (res.code == 200) {
              alert("手机验证成功");
              this.showSuccess = "pass";
            }
          })
          .catch(err => {
            alert("手机号码未注册");
            this.showSuccess = "show";
          });
      }
      // this.showSuccess = 'pass'
    },
    //点击下一步
    submitPsd() {
      console.log(this.userPsd);
      console.log(this.password_tow);
      var userPsd = this.userPsd;
      var password_tow = this.password_tow;
      if (userPsd != password_tow) {
        console.log("11111111111");
        this.errPsdInfoShowq = true;
        this.errPsdInfoShow = false;
      } else {
        this.errPsdInfoShow = false;
        this.errPsdInfoShowq = false;
        var data = {
          mobile: this.userp,
          newpassword: this.password_tow
        };
        this.$api
          .changepassword(data)
          .then(res => {
            if (res.code == 200) {
                alert("修改密码成功");
              this.showSuccess = "succ";
            } else {
              alert("修改密码错误");
              this.showSuccess = "pass";
            }
          })
          .catch(err => {
            alert("修改密码错误");
            this.showSuccess = "pass";
          });

        // var data ={

        // }
        // this.$api.changepassword(){

        // }
      }
      // if (!(/^1(3|4|5|6|7|8|9)\d{9}$/.test(this.userPhone))) {
      //     this.errPhoneInfoShow = true;
      //     return;
      // }
      // if (!this.codeNumInfo) {
      //     return;
      // }

      // if (!(/^[\w]{6,16}$/.test(this.userPsd))) {
      //     this.errPsdInfoShow = true;
      //     return;
      // }if(userPsd != password_tow){
      //     console.log("11111111111")
      // this.errPsdInfoShowq = true;
      //  this.errPsdInfoShow = false;
      // }
      // else {
      //     this.errPsdInfoShow = false;
      //     this.errPsdInfoShowq = false;
      // }
      // // 此处发送请求 回调提示成功

      // this.showSuccess = true;
    }
  }
};
</script>

<style lang="less" scoped>
.retrieve-password {
  width: 100%;
  height: 100%;
  .retrieve-container {
    width: 898px;
    height: 613px;
    border: 1px solid #e8e8e8;
    margin: 10px auto 0;
    box-shadow: 0 1px 2px 0 rgba(0, 0, 0, 0.1);
    .progress {
      width: 748px;
      height: 114px;
      margin: 43px auto 0;
      border-bottom: 1px solid #e8e8e8;
      user-select: none;
      .progress-circle,
      .gray-circle {
        width: 22px;
        height: 22px;
        background: #f3262d;
        border-radius: 50%;
        line-height: 22px;
        font-size: 14px;
        font-weight: 400;
        border: 5px solid #fbbdc0;
        color: #fff;
        float: left;
        // margin-left: 187px;
        margin-left: 27px;
      }
      .gray-circle {
        border-color: #e9ebef;
        background: #d0d5dd;
        margin: 0;
      }
      .success-circle {
        margin: 0;
      }
      .light-line,
      .gray-line {
        width: 126px;
        height: 1px;
        border-bottom: 3px dashed #f3262d;
        float: left;
        padding-top: 12px;
      }
      .gray-line {
        border-color: #ccc;
      }
      .progress-arrow {
        float: left;
        padding-top: 2px;
        font-size: 22px;
        padding: 2px 10px 0;
        color: #e7e7e7;
      }
      .lighe-arrow {
        color: #f3262d;
      }
      .progress-tips {
        clear: both;
        text-align: left;
        padding-top: 11px;
        font-weight: 400;
        .light-tips-item,
        .gray-tips-item {
          color: #f3262d;
          font-size: 14px;
          // margin-left: 150px;
        }
        .gray-tips-item {
          color: #999;
          margin: 0;
          float: right;
          // margin-right: 150px;
        }
        .success-tips {
          color: #f3262d;
        }
      }
    }
    .retrieve-input {
      width: 100%;
      .message-tips {
        width: 496px;
        height: 28px;
        margin: 22px auto 0;
        position: absolute;
        left: 50%;
        margin-left: -248px;
      }
      .form {
        text-align: left;
        width: 650px;
        min-height: 200px;
        position: absolute;
        left: 50%;
        margin-top: 83px;
        margin-left: -250px;
        box-sizing: border-box;

        .code {
          .item-inp {
            width: 151px;
          }
        }
      }
    }
    .succes_pass {
      display: block;
      /* text-align: left; */
      width: 100%;
      // height: 88px;
      margin: 57px auto;
      .span {
        display: block;
        width: 350px;
        margin: 0 auto 28px;
        text-align: left;
        font-size: 14px;
        color: #666;
      }
    }
    .success-retrieve {
      margin-top: 87px;
      .success-info {
        font-size: 14px;
        color: #26a571;
        font-weight: 400;
        > img {
          margin-right: 11px;
        }
        > span {
          position: relative;
          top: -6px;
        }
      }
      .login-again {
        margin-top: 40px;
        > span a {
          width: 100px;
          height: 36px;
          background: #f3262d;
          border-radius: 4px;
          display: inline-block;
          color: #fff;
          line-height: 36px;
          font-size: 14px;
          font-weight: 400;
          cursor: pointer;
          text-decoration: none;
          &:hover {
            background: #f43c42;
          }
        }
      }
    }
  }
}
</style>
<style lang="less" scoped>
.next-step {
  background: #f3262d;
  color: #fff;
  width: 100px;
  height: 36px;
  text-align: center;
  line-height: 36px;
  border-radius: 4px;
  font-size: 14px;
  display: inline-block;
  margin-top: 27px;
  cursor: pointer;
  &:hover {
    background: #f43c42;
  }
}
.item_1 {
  width: 603px;
  margin-left: 269px;
  text-align: left;
}
.item {
  margin-bottom: 22px;
  label {
    margin-right: 14px;
    font-size: 14px;
    color: #666;
    font-weight: 400;
    display: inline-block;
    width: 75px;
    text-align: right;
    padding-top: 10px;
  }
  .item-inp {
    width: 247px;
    padding-left: 10px;
    height: 36px;
    border: 1px solid #bdbdbd;
    vertical-align: top;
  }
  input::-webkit-input-placeholder {
    color: #999;
    font-size: 12px;
  }

  .get-code {
    width: 84px;
    height: 36px;
    border: 1px solid #dfdfdf;
    display: inline-block;
    font-size: 12px;
    color: #666;
    text-align: center;
    line-height: 36px;
    user-select: none;
    cursor: pointer;
    margin-left: 10px;
    position: relative;
    top: -2px;
    background: #fff;
    &:hover {
      background: #f4f4f4;
    }
  }
  .getting-code {
    background: #f4f4f4;
    cursor: not-allowed;
  }
}
.warning-tips {
  display: inline-block !important;
  vertical-align: -2px;
  .warning-icon {
    display: inline-block !important;
    vertical-align: 2px;
    width: 18px !important;
    height: 18px;
    color: #fff;
    background: #ffc600;
    border-radius: 50%;
    text-align: center !important;
    line-height: 18px;
    font-size: 18px;
    margin: 0 6px 0 14px;
  }
  .warning-info {
    color: #f3262d;
    font-size: 12px;
    font-weight: 400;
    vertical-align: 4px;
  }
}
</style>

