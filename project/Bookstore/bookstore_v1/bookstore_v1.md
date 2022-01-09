### 01-书城项目介绍(掌握)

* 项目介绍
  * ![image-20211201104055609](bookstore_v1.assets/image-20211201104055609.png)





### 02-项目一阶段搭建(掌握)

* 项目搭建
  * 将页面原型直接复制到bookstore-v1项目中





### 03-登录校验(掌握)

* 需求

  * 登录功能，实现账户、密码的非空校验

* 代码实现

  ```html
  <form action="login_success.html" @submit="checkLogin()">
      <label>用户名称：</label>
      <input
              class="itxt"
              type="text"
              placeholder="请输入用户名"
              autocomplete="off"
              tabindex="1"
              name="username"
              id="username"
              @change="checkUsername()"
              v-model="username"
      />
      <br/>
      <br/>
      <label>用户密码：</label>
      <input
              class="itxt"
              type="password"
              placeholder="请输入密码"
              autocomplete="off"
              tabindex="1"
              name="password"
              id="password"
              @change="checkPassword()"
              v-model="password"
      />
      <br/>
      <br/>
      <input type="submit" value="登录" id="sub_btn"/>
  </form>
  
  <script src="/bookstore-v1/static/script/vue.js"></script>
  <script>
  
      var vue = new Vue({
          el: "#app",
          data: {
              username: "",
              password: "",
              errorMsg: ""
          },
          methods: {
  
  
              checkNull(content, errorMsg) {
                  var reg = /^\s*$/;
                  if (reg.test(content)) {
                      //内容为空
                      this.errorMsg = errorMsg;
                      return false;
                  } else {
                      //内容不为空
                      this.errorMsg = "";
                      return true;
                  }
              },
  
              /**
               * 校验账户
               */
              checkUsername() {
                  return this.checkNull(this.username, "账户不能为空!");
              },
              /**
               * 校验密码
               */
              checkPassword() {
                  return this.checkNull(this.password, "密码不能为空!");
              },
  
              /**
               * 校验登录表单
               */
              checkLogin() {
                  //如果账户为空 || 密码为空，禁止表单提交
                  //账户为空 flag1=false
                  //密码为空 flag2=false
                  if (!this.checkUsername() || !this.checkPassword()) {
                      //禁止表单提交
                      event.preventDefault();
                  }
              }
          }
      })
  
  </script>
  ```





### 04-注册校验(掌握)

* 需求

  * 在注册功能中，对用户名、密码、确认密码、邮箱、验证码进行表单校验
    * 用户名应为6~16位数字和字母组成
    * 密码的长度至少为8位
    * 密码两次输入不一致
    * 请输入正确的邮箱格式
    * 验证码不能为空

* 代码实现

  ```html
  <form action="regist_success.html" @submit="checkRegist()">
      <div class="form-item">
          <div>
              <label>用户名称:</label>
              <input type="text" placeholder="请输入用户名" @change="checkUsername()" v-model="username"/>
          </div>
          <span class="errMess" v-text="usernameErrorMsg">用户名应为6~16位数字和字母组成</span>
      </div>
      <div class="form-item">
          <div>
              <label>用户密码:</label>
              <input type="password" placeholder="请输入密码" @change="checkPassword()" v-model="password"/>
          </div>
          <span class="errMess" v-text="passwordErrorMsg">密码的长度至少为5位</span>
      </div>
      <div class="form-item">
          <div>
              <label>确认密码:</label>
              <input type="password" placeholder="请输入确认密码" @change="checkRePassword()" v-model="repassword"/>
          </div>
          <span class="errMess" v-text="repasswordErrorMsg">密码两次输入不一致</span>
      </div>
      <div class="form-item">
          <div>
              <label>用户邮箱:</label>
              <input type="text" placeholder="请输入邮箱" @change="checkEmail()" v-model="email"/>
          </div>
          <span class="errMess" v-text="emailErrorMsg">请输入正确的邮箱格式</span>
      </div>
      <div class="form-item">
          <div>
              <label>验证码:</label>
              <div class="verify">
                  <input type="text" placeholder="" @change="checkCode()" v-model="code"/>
                  <img src="../../static/img/code.bmp" alt=""/>
              </div>
          </div>
          <span class="errMess" v-text="codeErrorMsg">请输入正确的验证码</span>
      </div>
      <button class="btn">注册</button>
  </form>
  
  ```