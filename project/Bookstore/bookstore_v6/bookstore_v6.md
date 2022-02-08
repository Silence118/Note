### 01-项目六阶段准备(掌握)

* 阶段准备
  * v5 -> v6
  * 直接复制





### 02-用户登录功能(掌握)

* 需求

  * 使用ajax完成用户登录功能

* 代码实现

  ```html
  <!--①禁用form原有的同步请求，②form校验通过发起异步请求-->
  <form action="user" @submit.prevent="checkLogin()" method="post">
  
  </form>
  <script src="static/script/vue.js"></script>
  <script src="static/script/axios.js"></script>
  <script>
  
      var vue = new Vue({
          el: "#app",
          data: {
              username: "",
              password: "",
              errorMsg: "[[${errorMsg}]]"
          },
          methods: {
  
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
                      return;
                  }
                  var _this = this;
                  //表单校验通过，发起异步请求
                  axios({
                      method: "post",
                      url: "user",
                      params: {
                          method: "login",
                          username: _this.username,
                          password: _this.password
                      }
                  }).then(function (res) {
                      var data = res.data;
                      console.log(data);
                      if (data.flag) {
                          //登录成功 , 跳转login_success.html
                          location.href = "user?method=toLoginSuccessPage";//同步请求
                      } else {
                          //登录失败 ,给出错误提示(局部刷新)
                          _this.errorMsg = data.msg;
  
                      }
  
                  });
              }
          }
      })
  
  </script>
  ```

  ```java
  /**
   * 用户登录
   *
   * @param request
   * @param response
   */
  public void login(HttpServletRequest request, HttpServletResponse response) {
      ResultVO resultVO = null;
      try {
          //告诉服务器以utf-8解码请求正文
          //request.setCharacterEncoding("utf-8");
  
          //处理请求
          String username = request.getParameter("username");
          String password = request.getParameter("password");
          UserService userService = new UserServiceImpl();
  
          User user = userService.login(username, password);
  
          //调度页面
  
          if (null != user) {
              //登录成功 ,将用户信息存储到session, 跳转到login_success.html (重定向)
              //告诉服务器已经登录
              request.getSession().setAttribute(BookstoreConstant.SESSION_KEY_USER, user);
              //response.sendRedirect(request.getContextPath() + "/user?method=toLoginSuccessPage");
              //告诉客户端已经登录
              resultVO = new ResultVO(true, BookstoreConstant.LOGIN_SUCCESS, user);
          } else {
              //登录失败 , 跳转回login.html (请求转发)
              //String errorMsg = "账户或密码错误!";
              //request.setAttribute("errorMsg", errorMsg);
              //toLoginPage(request, response);
              resultVO = new ResultVO(false, BookstoreConstant.LOGIN_FAIL, null);
  
          }
      } catch (Exception e) {
          e.printStackTrace();
          resultVO = new ResultVO(false, BookstoreConstant.LOGIN_FAIL, null);
      }
      try {
          JSONUtils.writeJavabean2ResponseText(response, resultVO);
      } catch (IOException e) {
          e.printStackTrace();
      }
  }
  ```







### 03-注销登录功能(掌握)

* 需求

  * 在index.html、login_success.html，点击注销按钮，发起ajax异步请求，实现注销功能。
  * 在注销操作之前，检查登录状态
    * 弹窗提示“您还没有登录!”、跳转到login.html页面

* 代码实现

  ```java
  /**
   * 注销登录功能
   *
   * @param request
   * @param response
   */
  public void logout(HttpServletRequest request, HttpServletResponse response) {
      //将保存在session的用户信息移除
      request.getSession().removeAttribute(BookstoreConstant.SESSION_KEY_USER);
      //移除成功之后
      ResultVO resultVO = new ResultVO(true, "注销登录成功!", null);
      try {
          JSONUtils.writeJavabean2ResponseText(response, resultVO);
      } catch (IOException e) {
          e.printStackTrace();
      }
  }
  ```

  ```html
  <a th:href="@{/user(method=logout)}" class="register" @click.prevent="logout()">注销</a>
  
  <script>
  	var vue = new Vue({
          el: "#app",
          data: {},
          methods: {
              logout() {
                  //检查登录状态
                  var jsObj = JSON.parse(sessionStorage.getItem("existUser"));
                  if (null == jsObj) {
                      //不在登录状态，给出提示，并跳转到login.html
                      alert("您还没有登录!");
                      location.href = "user?method=toLoginPage";
                      return;
                  }
  
  
                  //发起异步请求
                  axios({
                      method: "get",
                      url: "user",
                      params: {
                          method: "logout"
                      }
                  }).then(function (res) {
                      var data = res.data;
                      console.log(data);
                      if (data.flag) {
                          //注销登录成功，清空登录状态，跳转到index.html
                          sessionStorage.removeItem("existUser");
                          location.href = "index.html";
                      }
                  });
              }
          }
      })
  </script>
  ```

  



### 04-首页图书列表(掌握)

* 需求

  * index.html页面加载时，就请求图书列表并展示。

* 代码实现

  ```java
  @WebServlet("/index.html")
  public class IndexServlet extends ViewBaseServlet {
  
      @Override
      protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
          try {
              //转发到index.html
              processTemplate("index", req, resp);
          } catch (Exception e) {
              e.printStackTrace();
          }
  
      }
  
      @Override
      protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
          doGet(req, resp);
      }
  }
  ```

  ```html
  <div class="list-item" v-for="book in bookList">
      <img :src="book.imgPath" alt="">
      <p>书名:<span v-text="book.bookName">活着</span></p>
      <p>作者:<span v-text="book.author">余华</span></p>
      <p>价格:￥<span v-text="book.price">66.6</span></p>
      <p>销量:<span v-text="book.sales">230</span></p>
      <p>库存:<span v-text="book.stock">1000</span></p>
      <a href="">
          加入购物车
      </a>
  </div>
  
  
  <script>
      var swiper = new Swiper('.swiper-container', {
          autoplay: true,
          pagination: {
              el: '.swiper-pagination',
              dynamicBullets: true
          },
          navigation: {
              nextEl: '.swiper-button-next',
              prevEl: '.swiper-button-prev'
          }
      })
  
      var vue = new Vue({
          el: "#app",
          data: {
              bookList: []
          },
          methods: {
              /**
               * 注销登录
               */
              logout() {
                  //检查登录状态
                  var jsObj = JSON.parse(sessionStorage.getItem("existUser"));
                  if (null == jsObj) {
                      //不在登录状态，给出提示，并跳转到login.html
                      alert("您还没有登录!");
                      location.href = "user?method=toLoginPage";
                      return;
                  }
  
  
                  //发起异步请求
                  axios({
                      method: "get",
                      url: "user",
                      params: {
                          method: "logout"
                      }
                  }).then(function (res) {
                      var data = res.data;
                      console.log(data);
                      if (data.flag) {
                          //注销登录成功，清空登录状态，跳转到index.html
                          sessionStorage.removeItem("existUser");
                          location.href = "index.html";
                      }
                  });
              },
  
              /**
               * 获取图书列表
               */
              selectBookList() {
                  var _this = this;
                  axios({
                      method: "get",
                      url: "bookClient",
                      params: {
                          method: "selectBookList"
                      }
                  }).then(function (res) {
                      var data = res.data;
                      console.log(data);
                      if (data.flag) {
                          //展示图书列表
                          _this.bookList = data.data;
                      }
                  });
              }
  
          },
  
          mounted() {
              //发起异步请求，获取图书列表
              this.selectBookList();
  
          }
      })
  </script>
  ```





### 05-首页购物车总数量(掌握)

* 需求

  * 在index.html页面，不需要进行登录状态校验 
  * 在index.html中，页面加载时，使用ajax请求服务器，显示购物车总数量

* 代码实现

  ```html
  <div class="cart-num"
       v-text="totalCount">
      0
  </div>
  <script>
      var vue = new Vue({
          el: "#app",
          data: {
              bookList: [],
              totalCount: 0,//购物车总数量
          },
          methods: {
  
              /**
           * 查询购物车信息
           */
              selectCart() {
                  var _this = this;
                  axios({
                      method: "get",
                      url: "cart",
                      params: {
                          method: "selectCart"
                      }
                  }).then(function (res) {
                      var data = res.data;
                      console.log(data);
                      if (data.flag) {
                          //获取购物车总数量成功
                          var totalCount = data.data.totalCount;
                          _this.totalCount = totalCount;
                      }
  
                  });
              }
  
          },
  
          mounted() {
              //发起异步请求，获取图书列表
              this.selectBookList();
              //发起异步请求，获取购物车信息(总数量)
              this.selectCart();
          }
      })
  </script>
  
  ```

  ```java
  @WebServlet("/cart")
  public class CartServlet extends ModelBaseServlet {
  
      /**
       * 获取购物车信息
       *
       * @param request
       * @param response
       */
      public void selectCart(HttpServletRequest request, HttpServletResponse response) {
          Object existCart = request.getSession().getAttribute(BookstoreConstant.SESSION_KEY_CART);
          ResultVO resultVO = null;
          if (null == existCart) {
              //获取失败
              resultVO = new ResultVO(false, BookstoreConstant.QUERY_CART_FAIL, null);
          } else {
              //获取成功
              resultVO = new ResultVO(true, BookstoreConstant.QUERY_CART_SUCCESS, existCart);
          }
          try {
              JSONUtils.writeJavabean2ResponseText(response, resultVO);
          } catch (IOException e) {
              e.printStackTrace();
          }
      }
  
  
  }
  ```





### 06-首页加入购物车(掌握)

* 需求

  * 在index.html页面，不需要进行登录状态校验
  * 在index.html页面，点击加入购物车按钮，将对应图书加入购物车，并更新index.html页面 的购物车总数量

* 代码实现

  ```html
  <a href="" @click.prevent="addBook2Cart(book.bookId)">
      加入购物车
  </a>
  <script>
      var swiper = new Swiper('.swiper-container', {
          autoplay: true,
          pagination: {
              el: '.swiper-pagination',
              dynamicBullets: true
          },
          navigation: {
              nextEl: '.swiper-button-next',
              prevEl: '.swiper-button-prev'
          }
      })
  
      var vue = new Vue({
          el: "#app",
          data: {
              bookList: [],
              totalCount: 0,//购物车总数量
          },
          methods: {
  
  
              /**
               * 查询购物车信息
               */
              selectCart() {
                  var _this = this;
                  axios({
                      method: "get",
                      url: "cart",
                      params: {
                          method: "selectCart"
                      }
                  }).then(function (res) {
                      var data = res.data;
                      console.log(data);
                      if (data.flag) {
                          //获取购物车总数量成功
                          var totalCount = data.data.totalCount;
                          _this.totalCount = totalCount;
                      }
  
                  });
              },
  
              /**
               * 将图书加入到购物车
               */
              addBook2Cart(bookId) {
                  var _this = this;
                  axios({
                      method: "post",
                      url: "cart",
                      params: {
                          method: "addBook2Cart",
                          bookId: bookId
                      }
                  }).then(function (res) {
                      var data = res.data;
                      console.log(data);
                      if (data.flag) {
                          //添加购物车成功，获取购物车总数量
                          _this.selectCart();
                      }
                  });
              }
  
          },
  
          mounted() {
              //发起异步请求，获取图书列表
              this.selectBookList();
              //发起异步请求，获取购物车信息(总数量)
              this.selectCart();
          }
      })
  </script>
  ```

  ```java
  /**
   * 将图书添加到购物车
   *
   * @param request
   * @param response
   */
  public void addBook2Cart(HttpServletRequest request, HttpServletResponse response) {
      ResultVO resultVO = null;
      try {
          Integer bookId = Integer.parseInt(request.getParameter("bookId"));
          System.out.println("bookId = " + bookId);
          BookService bookService = new BookServiceImpl();
          Book book = bookService.selectBookById(bookId);
          //判断之前有没有购物车
          Cart cart = (Cart) request.getSession().getAttribute(BookstoreConstant.SESSION_KEY_CART);
          if (null == cart) {
              //之前没有购物车
              cart = new Cart();
          } else {
              //之前有购物车
          }
          cart.addBook2Cart(book);
          request.getSession().setAttribute(BookstoreConstant.SESSION_KEY_CART, cart);
          resultVO = new ResultVO(true, BookstoreConstant.CART_ADD_SUCCESS, null);
      } catch (Exception e) {
          e.printStackTrace();
          resultVO = new ResultVO(false, BookstoreConstant.CART_ADD_FAIL, null);
  
      }
  
      try {
          JSONUtils.writeJavabean2ResponseText(response, resultVO);
      } catch (IOException e) {
          e.printStackTrace();
      }
  }
  ```







### 07-跳转购物车页面(掌握)

* 需求

  * 在index.html页面中，点击购物车按钮，跳转到cart.html页面
  * 需要进行登录状态校验

* 代码实现

  ```html
  <a
          th:href="@{/protected/cart(method=toCartPage)}"
          class="cart iconfont icon-gouwuche">
      购物车
      <div class="cart-num"
           v-text="totalCount">
          0
      </div>
  
  </a>
  ```

  ```java
  @WebServlet("/protected/cart")
  public class ProtectedCartServlet extends ModelBaseServlet {
  
  
  
      /**
       * 转发到cart.html
       *
       * @param request
       * @param response
       */
      public void toCartPage(HttpServletRequest request, HttpServletResponse response) {
          try {
              //获取购物车信息
              //Object existCart = request.getSession().getAttribute(BookstoreConstant.SESSION_KEY_CART);
              processTemplate("pages/cart/cart", request, response);
          } catch (IOException e) {
              e.printStackTrace();
          }
      }
  }
  ```





### 08-跳转购物车页面的登录状态校验(掌握)

* 分析
  * 在index.html页面，点击购物车，发起同步请求，需要服务器返回一个页面文件，
  * 如果在登录状态，服务器返回给浏览器的是cart.html
  * 如果不在登录状态，服务器返回给浏览器的是login.html





### 09-显示购物车列表(掌握)

* 需求

  * 在cart.html页面中，使用ajax异步请求，显示购物车列表
  * 需要进行登录状态校验

* 代码实现

  ```html
  <div class="w">
  
      <!--购物车项列表-->
      <table>
          <thead>
              <tr>
                  <th>图片</th>
                  <th>商品名称</th>
  
                  <th>数量</th>
                  <th>单价</th>
                  <th>金额</th>
                  <th>操作</th>
              </tr>
          </thead>
          <!-- 购物车为空时 -->
          <tbody v-if="cartIsNull || cart.totalCount == 0">
              <tr>
                  <td colspan="6">
                      <a href="index.html">购物车空空如也，请抓紧购物吧!!!!</a>
                  </td>
              </tr>
          </tbody>
          <!-- 购物车不为空时 -->
          <tbody v-if="!(cartIsNull || cart.totalCount == 0)">
              <!--cartItemEntry : 键值对对象(键:bookId, 值:CartItem对象)-->
              <tr v-for="cartItem in cart.cartItemMap">
                  <td>
                      <img :src="cartItem.imgPath" alt=""/>
                  </td>
                  <td v-text="cartItem.bookName">活着</td>
                  <td>
                      <span class="count">
                          <a href="">-</a>
                      </span>
                      <input class="count-num" type="text" :value="cartItem.count"/>
  
                      <span class="count">
                          <a href="">
                              +
                          </a>
                      </span>
                  </td>
                  <td v-text="cartItem.price">36.8</td>
                  <td v-text="cartItem.amount">36.8</td>
                  <td>
                      <a href="">
                          删除
                      </a>
                  </td>
              </tr>
  
          </tbody>
      </table>
      <!--展示商品总数量、总金额-->
      <div class="footer">
          <div class="footer-left">
              <a th:href="@{/protected/cart(method=clearCart)}" class="clear-cart">清空购物车</a>
              <a href="#">继续购物</a>
          </div>
  
          <!--购物车为空-->
          <div class="footer-right" v-if="cartIsNull || cart.totalCount == 0">
              <div>共<span>0</span>件商品</div>
              <div class="total-price">总金额<span>0.0</span>元</div>
              <a class="pay" th:href="@{/protected/orderClient(method=checkout)}">去结账</a>
          </div>
          <!--购物车不为空-->
          <div class="footer-right" v-if="!(cartIsNull || cart.totalCount == 0)">
              <div>共<span v-text="cart.totalCount">3</span>件商品</div>
              <div class="total-price">总金额<span v-text="cart.totalAmount">99.9</span>元</div>
              <a class="pay" th:href="@{/protected/orderClient(method=checkout)}">去结账</a>
          </div>
      </div>
  </div>
  
  <script>
  
      var vue = new Vue({
          el: "#app",
          data: {
              cart: {},
              cartIsNull: true
          },
          methods: {
  
              /**
               * 查询购物车信息
               */
              selectCart() {
                  var _this = this;
                  axios({
                      method: "get",
                      url: "protected/cart",
                      params: {
                          method: "selectCart"
                      }
                  }).then(function (res) {
                      var data = res.data;
                      console.log(data);
                      _this.cartIsNull = !data.flag;
                      if (data.flag) {
                          //获取购物车总数量成功
                          _this.cart = data.data;
  
                      }
  
                  });
              }
          },
          mounted() {
              //发起异步请求，获取购物车信息
              this.selectCart();
          }
      })
  
  </script>
  ```

  ```java
  @WebServlet("/protected/cart")
  public class ProtectedCartServlet extends ModelBaseServlet {
  
  
      /**
       * 获取购物车信息
       *
       * @param request
       * @param response
       */
      public void selectCart(HttpServletRequest request, HttpServletResponse response) {
          Object existCart = request.getSession().getAttribute(BookstoreConstant.SESSION_KEY_CART);
          ResultVO resultVO = null;
          if (null == existCart) {
              //获取失败
              resultVO = new ResultVO(false, BookstoreConstant.QUERY_CART_FAIL, null);
          } else {
              //获取成功
              resultVO = new ResultVO(true, BookstoreConstant.QUERY_CART_SUCCESS, existCart);
          }
          try {
              JSONUtils.writeJavabean2ResponseText(response, resultVO);
          } catch (IOException e) {
              e.printStackTrace();
          }
      }
  }
  ```

  





### 10-清空购物车(掌握)

* 概述

  * 在cart.html页面，点击清空购物车按钮，执行清空购物车操作。
  * 需要进行登录状态校验

* 代码实现

  ```html
  <script>
  /**
   * 清空购物车
   */
  clearCart() {
  
      var existUser = JSON.parse(sessionStorage.getItem("existUser"));
      if (null == existUser) {
          //不在登录状态
          alert("您还没有登录!")
          location.href = "user?method=toLoginPage";
          return;
      }
  
  
      var _this = this;
  
      var confirm = window.confirm("确认清空购物车吗?");
      if (confirm) {
          axios({
              method: "get",
              url: "protected/cart",
              params: {
                  method: "clearCart"
              }
          }).then(function (res) {
              var data = res.data;
              console.log(data);
              if (data.flag) {
                  //清空成功，重新获取购物车信息
                  //_this.selectCart();
                  location.reload();
              }
  
          });
      }
  
  
  }
  </script>
  
  ```

  ```java
  /**
   * 清空购物车
   *
   * @param request
   * @param response
   */
  public void clearCart(HttpServletRequest request, HttpServletResponse response) {
      ResultVO resultVO = null;
      Object existCart = request.getSession().getAttribute(BookstoreConstant.SESSION_KEY_CART);
      if (null == existCart) {
          //清空失败
          resultVO = new ResultVO(false, BookstoreConstant.CLEAR_CART_FAIL, null);
      } else {
          //清空成功
          request.getSession().removeAttribute(BookstoreConstant.SESSION_KEY_CART);
          resultVO = new ResultVO(true, BookstoreConstant.CLEAR_CART_SUCCESS, null);
      }
  
      try {
          JSONUtils.writeJavabean2ResponseText(response, resultVO);
      } catch (IOException e) {
          e.printStackTrace();
      }
  
  }
  ```





### 11-删除指定购物车项(掌握)

* 需求

  * 在cart.html页面，点击删除按钮，使用ajax完成删除指定购物项
  * 需要进行登录状态校验

* 代码实现

  ```html
  <a href="" @click.prevent="removeCartItem(cartItem.bookId)">
      删除
  </a>
  <script>
  
      removeCartItem(bookId) {
  
          var confirm = window.confirm("确认删除指定购物车项?");
          if (confirm) {
              var _this = this;
              axios({
                  method: "get",
                  url: "protected/cart",
                  params: {
                      method: "removeCartItem",
                      bookId: bookId
                  }
              }).then(function (res) {
                  var data = res.data;
                  console.log(data);
                  if (data.flag) {
                      //移除成功，重新获取购物车信息
                      _this.selectCart();
                      //location.reload();
                  }
  
              });
          }
      }
  </script>
  ```

  ```java
  /**
   * 删除指定购物车项
   *
   * @param request
   * @param response
   */
  public void removeCartItem(HttpServletRequest request, HttpServletResponse response) {
      Integer bookId = Integer.parseInt(request.getParameter("bookId"));
      ResultVO resultVO = null;
      //获取指定bookId的购物车项数量
      Cart cart = (Cart) request.getSession().getAttribute(BookstoreConstant.SESSION_KEY_CART);
      if (cart == null) {
          resultVO = new ResultVO(false, BookstoreConstant.REMOVE_CARTITEM_FAIL, null);
      } else {
          cart.removeCartItem(bookId);
          resultVO = new ResultVO(true, BookstoreConstant.REMOVE_CARTITEM_SUCCESS, null);
  
      }
      //
      try {
          JSONUtils.writeJavabean2ResponseText(response, resultVO);
      } catch (IOException e) {
          e.printStackTrace();
      }
  
  }
  ```







### 12-指定购物车项数量减一(掌握)

* 需求

  * 在cart.html页面，点击"-"按钮，使用ajax，指定购物车项数量减一
  * 需要进行登录状态校验

* 代码实现

  ```html
  <a href="" @click.prevent="cartItemCountDecrease(cartItem.bookId,cartItem.count)">
      -
  </a>
  <script>
      /**
      * 指定购物车项数量减一
      * @param bookId
      */
      cartItemCountDecrease(bookId, count) {
          var _this = this;
          //校验当前数量是否为1
          if (count <= 1) {
              //当前数量=1 ，直接移除
              _this.removeCartItem(bookId);
          } else {
              //当前数量!=1 异步请求cartItemCountDecrease
              axios({
                  method: "get",
                  url: "protected/cart",
                  params: {
                      method: "cartItemCountDecrease",
                      bookId: bookId
                  }
              }).then(function (res) {
                  var data = res.data;
                  console.log(data);
                  if (data.flag) {
                      //减一成功
                      _this.selectCart();
                      //location.reload();
                  }
  
              });
          }
  
      }
  </script>
  ```

  ```java
  /**
   * 指定购物车项数量减一
   *
   * @param request
   * @param response
   */
  public void cartItemCountDecrease(HttpServletRequest request, HttpServletResponse response) {
      Integer bookId = Integer.parseInt(request.getParameter("bookId"));
      //获取指定bookId的购物车项数量
      ResultVO resultVO = null;
      Cart cart = (Cart) request.getSession().getAttribute(BookstoreConstant.SESSION_KEY_CART);
      if (null == cart) {
          resultVO = new ResultVO(false,BookstoreConstant.CARTITEM_COUNT_DECREASE_FAIL,null);
      } else {
          CartItem cartItem = cart.getCartItemMap().get(bookId);
          if (cartItem.getCount() <= 1) {
              //直接移除购物车项
              cart.removeCartItem(bookId);
          } else {
              //购物车项数量减一
              cart.cartItemCountDecrease(bookId);
          }
          resultVO = new ResultVO(true,BookstoreConstant.CARTITEM_COUNT_DECREASE_SUCCESS,null);
  
      }
      try {
          JSONUtils.writeJavabean2ResponseText(response, resultVO);
      } catch (IOException e) {
          e.printStackTrace();
      }
  
  }
  ```





### 13-指定购物车项数量加一(掌握)

* 需求

  * 在cart.html页面，点击"+"按钮，使用ajax，指定购物车项数量加一
  * 需要进行登录状态校验

* 代码实现

  ```html
  <script>
      /**
               * 获取登录状态
               * @returns {boolean}
               */
      isLogin(){
          var existUser = JSON.parse(sessionStorage.getItem("existUser"));
          if (null == existUser) {
              //不在登录状态
              alert("您还没有登录!")
              location.href = "user?method=toLoginPage";
              return false;
          } else {
              return true;
          }
      },
  
          /**
               * 指定购物车项数量加一
               */
          cartItemCountIncrease(bookId){
              if (!this.isLogin()) {
                  return;
              }
              var _this = this;
              axios({
                  method: "get",
                  url: "protected/cart",
                  params: {
                      method: "cartItemCountIncrease",
                      bookId: bookId
                  }
              }).then(function (res) {
                  var data = res.data;
                  console.log(data);
                  if (data.flag) {
                      //加一成功
                      _this.selectCart();
                      //location.reload();
                  }
  
              });
          }
  </script>
  ```

  ```java
  /**
   * 指定购物车项数量加一
   *
   * @param request
   * @param response
   */
  public void cartItemCountIncrease(HttpServletRequest request, HttpServletResponse response) {
      Integer bookId = Integer.parseInt(request.getParameter("bookId"));
      //获取指定bookId的购物车项数量
      Cart cart = (Cart) request.getSession().getAttribute(BookstoreConstant.SESSION_KEY_CART);
      ResultVO resultVO = null;
  
      if (null == cart) {
          resultVO = new ResultVO(false,BookstoreConstant.CARTITEM_COUNT_INCREASE_FAIL,null);
  
      } else {
          cart.cartItemCountIncrease(bookId);
          resultVO = new ResultVO(true,BookstoreConstant.CARTITEM_COUNT_INCREASE_SUCCESS,null);
  
      }
      //
      try {
          JSONUtils.writeJavabean2ResponseText(response, resultVO);
      } catch (IOException e) {
          e.printStackTrace();
      }
  
  }
  ```



### 14-修改购物车项数量(掌握)

* 需求

  * 在cart.html页面，输入新的数量，使用ajax，修改指定购物车项的数量
  * 需要进行登录状态校验

* 代码实现

  ```html
  <input class="count-num" type="text" :value="cartItem.count"
                                 @change="updateCartItemCount(cartItem.bookId)"/>
  <script>
      /**
   * 修改指定购物车项数量
   * @param bookId
   */
  updateCartItemCount(bookId) {
  
      var newCount = event.target.value;
      console.log("newCount : " + newCount);
      if (newCount <= 0) {
          //删除指定购物车项
          this.removeCartItem(bookId);
      } else {
          var _this = this;
          axios({
              method: "get",
              url: "protected/cart",
              params: {
                  method: "updateCartItemCount",
                  bookId: bookId,
                  newCount: newCount
              }
          }).then(function (res) {
              var data = res.data;
              console.log(data);
              _this.cartIsNull = !data.flag;
              if (data.flag) {
                  //修改购物车项数量成功，重新获取购物车信息
                  _this.selectCart();
  
              }
  
          });
  
      }
  
  }
  </script>
  
  ```

  ```java
  /**
   * 修改购物车项数量
   *
   * @param request
   * @param response
   */
  public void updateCartItemCount(HttpServletRequest request, HttpServletResponse response) {
      Integer bookId = Integer.parseInt(request.getParameter("bookId"));
      Integer newCount = Integer.parseInt(request.getParameter("newCount"));
      Cart cart = (Cart) request.getSession().getAttribute(BookstoreConstant.SESSION_KEY_CART);
      ResultVO resultVO = null;
      if (null == cart) {
          //修改失败
          resultVO = new ResultVO(false, BookstoreConstant.UPDATE_CARTITEM_COUNT_FAIL, null);
  
      } else {
          //修改成功
          if (newCount <= 0) {
              //直接删除购物车项
              cart.removeCartItem(bookId);
          } else {
              //修改购物车项数量
              cart.updateCartItemCount(bookId, newCount);
          }
          resultVO = new ResultVO(true, BookstoreConstant.UPDATE_CARTITEM_COUNT_SUCCESS, null);
  
      }
      try {
          JSONUtils.writeJavabean2ResponseText(response, resultVO);
      } catch (IOException e) {
          e.printStackTrace();
      }
  
  }
  ```





### 15-ModelBaseServlet优化(掌握)

* 问题分析

* 代码实现

  ```java
  public class ModelBaseServlet extends ViewBaseServlet {
      @Override
      protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
          //method=addUser
          String methodName = request.getParameter("method");
          try {
              Method method = this.getClass().getMethod(methodName, HttpServletRequest.class, HttpServletResponse.class);
              Object result = method.invoke(this, request, response);
              boolean present = method.isAnnotationPresent(MyResponseBody.class);
              if (present) {
                  if (null != result) {
                      //方法执行之后返回的是javabean对象
                      JSONUtils.writeJavabean2ResponseText(response, result);
                  }
              } else {
                  //没有使用@MyResponseBody注解，转发/重定向
                  String newResult = (String) result;
                  if (newResult != null) {
                      if (newResult.startsWith("forward:")) {
                          //forward:pages/cart/cart -> pages/cart/cart
                          String path = newResult.substring("forward:".length());
                          processTemplate(path, request, response);
                      } else if (newResult.startsWith("redirect:")) {
                          //redirect:index.html -> index.html
                          // /项目访问路径/资源访问路径
                          String path = newResult.substring("redirect:".length());
                          response.sendRedirect(request.getContextPath() + "/" + path);
                      } else {
                          processTemplate(newResult, request, response);
                      }
                  }
  
              }
          } catch (Exception e) {
              e.printStackTrace();
          }
      }
  
      @Override
      protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
          doGet(request, response);
      }
  }
  ```

  ```java
  @WebServlet("/protected/cart")
  public class ProtectedCartServlet extends ModelBaseServlet {
  
  
      /**
       * 获取购物车信息
       *
       * @param request
       * @param response
       */
      @MyResponseBody
      public Object selectCart(HttpServletRequest request, HttpServletResponse response) {
          Object existCart = request.getSession().getAttribute(BookstoreConstant.SESSION_KEY_CART);
          ResultVO resultVO = null;
          if (null == existCart) {
              //获取失败
              resultVO = new ResultVO(false, BookstoreConstant.QUERY_CART_FAIL, null);
          } else {
              //获取成功
              resultVO = new ResultVO(true, BookstoreConstant.QUERY_CART_SUCCESS, existCart);
          }
  //        try {
  //            JSONUtils.writeJavabean2ResponseText(response, resultVO);
  //        } catch (IOException e) {
  //            e.printStackTrace();
  //        }
          return resultVO;//如果return是一个javabean对象，意味着要将其转换为json字符串，并作为响应正文返回给浏览器
      }
  
      /**
       * 转发到cart.html
       *
       * @param request
       * @param response
       */
      public String toCartPage(HttpServletRequest request, HttpServletResponse response) {
          //try {
              //获取购物车信息
              //Object existCart = request.getSession().getAttribute(BookstoreConstant.SESSION_KEY_CART);
              //processTemplate("pages/cart/cart", request, response);
          //} catch (IOException e) {
              //e.printStackTrace();
          //}
          //return "pages/cart/cart";
          return "forward:pages/cart/cart";
      }
  
  
      /**
       * 将图书添加到购物车
       *
       * @param request
       * @param response
       */
      public String addBook2Cart(HttpServletRequest request, HttpServletResponse response) {
  
  
          try {
              Integer bookId = Integer.parseInt(request.getParameter("bookId"));
              System.out.println("bookId = " + bookId);
              BookService bookService = new BookServiceImpl();
              Book book = bookService.selectBookById(bookId);
              //判断之前有没有购物车
              Cart cart = (Cart) request.getSession().getAttribute(BookstoreConstant.SESSION_KEY_CART);
              if (null == cart) {
                  //之前没有购物车
                  cart = new Cart();
              } else {
                  //之前有购物车
              }
              cart.addBook2Cart(book);
              request.getSession().setAttribute(BookstoreConstant.SESSION_KEY_CART, cart);
  
              //购物车添加完成 , 跳转到index.html (重定向)
              //response.sendRedirect(request.getContextPath() + "/index.html");
  
          } catch (Exception e) {
              e.printStackTrace();
          }
  
          return "redirect:index.html";
      }
  
  
      /**
       * 清空购物车
       *
       * @param request
       * @param response
       */
      @MyResponseBody
      public Object clearCart(HttpServletRequest request, HttpServletResponse response) {
          ResultVO resultVO = null;
          Object existCart = request.getSession().getAttribute(BookstoreConstant.SESSION_KEY_CART);
          if (null == existCart) {
              //清空失败
              resultVO = new ResultVO(false, BookstoreConstant.CLEAR_CART_FAIL, null);
          } else {
              //清空成功
              request.getSession().removeAttribute(BookstoreConstant.SESSION_KEY_CART);
              resultVO = new ResultVO(true, BookstoreConstant.CLEAR_CART_SUCCESS, null);
          }
  
          return resultVO;
  
      }
  
  
      /**
       * 指定购物车项数量减一
       *
       * @param request
       * @param response
       */
      @MyResponseBody
      public Object cartItemCountDecrease(HttpServletRequest request, HttpServletResponse response) {
          Integer bookId = Integer.parseInt(request.getParameter("bookId"));
          //获取指定bookId的购物车项数量
          ResultVO resultVO = null;
          Cart cart = (Cart) request.getSession().getAttribute(BookstoreConstant.SESSION_KEY_CART);
          if (null == cart) {
              resultVO = new ResultVO(false,BookstoreConstant.CARTITEM_COUNT_DECREASE_FAIL,null);
          } else {
              CartItem cartItem = cart.getCartItemMap().get(bookId);
              if (cartItem.getCount() <= 1) {
                  //直接移除购物车项
                  cart.removeCartItem(bookId);
              } else {
                  //购物车项数量减一
                  cart.cartItemCountDecrease(bookId);
              }
              resultVO = new ResultVO(true,BookstoreConstant.CARTITEM_COUNT_DECREASE_SUCCESS,null);
  
          }
          return resultVO;
  
      }
  
  
      /**
       * 指定购物车项数量加一
       *
       * @param request
       * @param response
       */
      @MyResponseBody
      public Object cartItemCountIncrease(HttpServletRequest request, HttpServletResponse response) {
          Integer bookId = Integer.parseInt(request.getParameter("bookId"));
          //获取指定bookId的购物车项数量
          Cart cart = (Cart) request.getSession().getAttribute(BookstoreConstant.SESSION_KEY_CART);
          ResultVO resultVO = null;
  
          if (null == cart) {
              resultVO = new ResultVO(false,BookstoreConstant.CARTITEM_COUNT_INCREASE_FAIL,null);
  
          } else {
              cart.cartItemCountIncrease(bookId);
              resultVO = new ResultVO(true,BookstoreConstant.CARTITEM_COUNT_INCREASE_SUCCESS,null);
  
          }
          //
          return resultVO;
  
      }
  
  
      /**
       * 删除指定购物车项
       *
       * @param request
       * @param response
       */
      @MyResponseBody
      public Object removeCartItem(HttpServletRequest request, HttpServletResponse response) {
          Integer bookId = Integer.parseInt(request.getParameter("bookId"));
          ResultVO resultVO = null;
          //获取指定bookId的购物车项数量
          Cart cart = (Cart) request.getSession().getAttribute(BookstoreConstant.SESSION_KEY_CART);
          if (cart == null) {
              resultVO = new ResultVO(false, BookstoreConstant.REMOVE_CARTITEM_FAIL, null);
          } else {
              cart.removeCartItem(bookId);
              resultVO = new ResultVO(true, BookstoreConstant.REMOVE_CARTITEM_SUCCESS, null);
  
          }
          //
          return resultVO;
  
      }
  
  
      /**
       * 修改购物车项数量
       *
       * @param request
       * @param response
       */
      @MyResponseBody
      public Object updateCartItemCount(HttpServletRequest request, HttpServletResponse response) {
          Integer bookId = Integer.parseInt(request.getParameter("bookId"));
          Integer newCount = Integer.parseInt(request.getParameter("newCount"));
          Cart cart = (Cart) request.getSession().getAttribute(BookstoreConstant.SESSION_KEY_CART);
          ResultVO resultVO = null;
          if (null == cart) {
              //修改失败
              resultVO = new ResultVO(false, BookstoreConstant.UPDATE_CARTITEM_COUNT_FAIL, null);
  
          } else {
              //修改成功
              if (newCount <= 0) {
                  //直接删除购物车项
                  cart.removeCartItem(bookId);
              } else {
                  //修改购物车项数量
                  cart.updateCartItemCount(bookId, newCount);
              }
              resultVO = new ResultVO(true, BookstoreConstant.UPDATE_CARTITEM_COUNT_SUCCESS, null);
  
          }
          return resultVO;
  
      }
  
  
  }
  ```