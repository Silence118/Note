### 01-ModelBaseServlet类(难点)

* 概述

  * 之前，一个业务请求就对应一个Servlet，这样就导致有特别多的Servlet，项目过于冗余了。可以使用ModelBaseServlet来解决。

* 代码实现

  ```java
  public class ModelBaseServlet extends ViewBaseServlet {
      @Override
      protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
          //method=addUser
          String methodName = request.getParameter("method");
          try {
              Method method = this.getClass().getMethod(methodName, HttpServletRequest.class, HttpServletResponse.class);
              method.invoke(this, request, response);
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
  @WebServlet("/user")
  public class UserServlet extends ModelBaseServlet {
  
      public void addUser(HttpServletRequest request, HttpServletResponse response) throws IOException {
          System.out.println("UserServlet addUser");
          String username = request.getParameter("username");
          System.out.println("username = " + username);
          String password = request.getParameter("password");
          System.out.println("password = " + password);
          //向浏览器发送响应正文“helloworld”
          response.getWriter().write("helloworld");
      }
  
      public void deleteUser(HttpServletRequest request, HttpServletResponse response) {
          System.out.println("UserServlet deleteUser");
      }
  
      public void updateUser(HttpServletRequest request, HttpServletResponse response) {
          System.out.println("UserServlet updateUser");
      }
  
      public void selectUser(HttpServletRequest request, HttpServletResponse response) {
          System.out.println("UserServlet selectUser");
      }
  
  }
  ```

  ```html
  <!DOCTYPE html>
  <html lang="en" xmlns:th="http://www.thymeleaf.org">
  <head>
      <meta charset="UTF-8">
      <base th:href="@{/}">
      <title>ModelBaseServlet类</title>
  </head>
  <body>
  
  <a href="user?method=addUser&username=root&password=12345">添加用户</a>
  <a href="user?method=deleteUser">删除用户</a>
  <a href="user?method=updateUser">修改用户</a>
  <a href="user?method=selectUser">查询用户</a>
  
  </body>
  </html>
  ```





### 02-项目三阶段准备(掌握)

* 项目搭建
  * v2 -> v3
  * 将pages目录和index.html转译到WEB-INF目录





### 03-书城首页功能(掌握)

* 开发步骤

  * ①引入thymeleaf相关jar包
  * ②引入ViewBaseServlet、ModelBaseServlet
  * ③定义IndexServlet，访问路径为"/index.html"
    * 获取所有图书列表
    * 请求转发到页面index.html
  * ④编写index.html页面，展示图书列表

* ①引入thymeleaf相关jar包

* ②引入ViewBaseServlet、ModelBaseServlet

* ③定义IndexServlet，访问路径为"/index.html"

  ```java
  @WebServlet("/index.html")
  public class IndexServlet extends ViewBaseServlet {
  
      @Override
      protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
          //获取所有图书列表
          BookService bookService = new BookServiceImpl();
          List<Book> bookList = null;
          try {
              bookList = bookService.selectBookList();
              req.setAttribute("bookList", bookList);
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

* ④编写index.html页面，展示图书列表

  ```html
  <div class="list-content">
  
      <div class="list-item" th:each="book:${bookList}">
          <img th:src="${book.imgPath}" alt="">
          <p>书名:<span th:text="${book.bookName}">活着</span></p>
          <p>作者:<span th:text="${book.author}">余华</span></p>
          <p>价格:￥<span th:text="${book.price}">66.6</span></p>
          <p>销量:<span th:text="${book.sales}">230</span></p>
          <p>库存:<span th:text="${book.stock}">1000</span></p>
          <button>加入购物车</button>
      </div>
  
  
  </div>
  ```





### 04-优化登录功能(掌握)

* 需求

  * 使用ModelBaseServlet，添加"账户或密码错误!"提示

* 开发步骤

  * ①定义UserServlet，继承于ModelBaseSerlvet
    * 转发到login.html
    * 转发到login_success.html
    * 用户登录功能
  * ②编写login.html
    * 使用thymeleaf

* ①定义UserServlet，继承于ModelBaseSerlvet

  ```java
  @WebServlet("/user")
  public class UserServlet extends ModelBaseServlet {
  
  
      /**
       * 转发到login.html
       *
       * @param request
       * @param response
       */
      public void toLoginPage(HttpServletRequest request, HttpServletResponse response) {
          try {
              processTemplate("pages/user/login", request, response);
          } catch (IOException e) {
              e.printStackTrace();
          }
      }
  
      /**
       * 转发到login_success.html
       *
       * @param request
       * @param response
       */
      public void toLoginSuccessPage(HttpServletRequest request, HttpServletResponse response) {
          try {
              processTemplate("pages/user/login_success", request, response);
          } catch (IOException e) {
              e.printStackTrace();
          }
      }
  
      /**
       * 用户登录
       *
       * @param request
       * @param response
       */
      public void login(HttpServletRequest request, HttpServletResponse response) {
          //处理请求
          String username = request.getParameter("username");
          String password = request.getParameter("password");
          UserService userService = new UserServiceImpl();
          try {
              User user = userService.login(username, password);
              //调度页面
              if (null != user) {
                  //登录成功 , 跳转到login_success.html (重定向)
                  response.sendRedirect(request.getContextPath() + "/user?method=toLoginSuccessPage");
              } else {
                  //登录失败 , 跳转回login.html (请求转发)
                  String errorMsg = "账户或密码错误!";
                  request.setAttribute("errorMsg", errorMsg);
                  toLoginPage(request, response);
              }
          } catch (Exception e) {
              e.printStackTrace();
          }
      }
  
  
  }
  ```

* ②编写login.html

  ```html
  <form action="user" @submit="checkLogin()">
      <input type="hidden" name="method" value="login">
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
  ```

* 存在问题

  * th:text="${errorMsg}"无法正常显示，因为被v-text="errorMsg"覆盖了.





### 05-优化注册功能(掌握)

* 需求

  * 使用ModelBaseServlet，添加"用户名已经存在"提示

* 开发步骤

  * ①修改UserServlet
    * 转发到regist.html
    * 转发到regist_success.html
    * 用户注册功能
  * ②编写regist.html
    * 使用thymeleaf

* ①修改UserServlet

  ```java
  @WebServlet("/user")
  public class UserServlet extends ModelBaseServlet {
  
  
  
      /**
       * 转发到regist.html
       *
       * @param request
       * @param response
       */
      public void toRegistPage(HttpServletRequest request, HttpServletResponse response) {
          try {
              processTemplate("pages/user/regist", request, response);
          } catch (IOException e) {
              e.printStackTrace();
          }
      }
  
      /**
       * 转发到regist_success.html
       *
       * @param request
       * @param response
       */
      public void toRegistSuccessPage(HttpServletRequest request, HttpServletResponse response) {
          try {
              processTemplate("pages/user/regist_success", request, response);
          } catch (IOException e) {
              e.printStackTrace();
          }
      }
  
      /**
       * 用户注册
       *
       * @param req
       * @param resp
       */
      public void regist(HttpServletRequest req, HttpServletResponse resp) {
          //使用BeanUtils自动封装
          User inputUser = new User();
          try {
              BeanUtils.populate(inputUser, req.getParameterMap());
              System.out.println("inputUser = " + inputUser);
              UserService userService = new UserServiceImpl();
              //校验用户名是否存在
              User dbUser = userService.selectUserByUsername(inputUser.getUserName());
              if (null != dbUser) {
                  //注册失败，用户名已经存在 , 跳转回regist.html (转发)
                  resp.setContentType("text/html;charset=utf-8");
                  String errorMsg = "用户名已经存在!";
                  req.setAttribute("errorMsg", errorMsg);
                  toRegistPage(req, resp);
                  return;
              }
  
              int addUser = userService.addUser(inputUser);
              if (addUser > 0) {
                  //添加成功 , 跳转到regist_success.html (重定向)
                  resp.sendRedirect(req.getContextPath() + "/user?method=toRegistSuccessPage");
              } else {
                  //添加失败 , 跳转回regist.html (转发)
                  toLoginPage(req, resp);
              }
          } catch (Exception e) {
              e.printStackTrace();
          }
      }
  
  
  }
  ```

* ②编写regist.html

  ```html
  <form action="user" @submit="checkRegist()">
      <input type="hidden" name="method" value="regist">
      <div class="form-item">
          <div>
              <label>用户名称:</label>
              <input type="text" placeholder="请输入用户名" @change="checkUsername()" v-model="username"
                     name="userName"/>
          </div>
          <span class="errMess" v-text="usernameErrorMsg" th:text="${errorMsg}">用户名应为6~16位数字和字母组成</span>
      </div>
      <div class="form-item">
          <div>
              <label>用户密码:</label>
              <input type="password" placeholder="请输入密码" @change="checkPassword()" v-model="password"
                     name="userPwd"/>
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
              <input type="text" placeholder="请输入邮箱" @change="checkEmail()" v-model="email" name="email"/>
          </div>
          <span class="errMess" v-text="emailErrorMsg">请输入正确的邮箱格式</span>
      </div>
      <div class="form-item">
          <div>
              <label>验证码:</label>
              <div class="verify">
                  <input type="text" placeholder="" @change="checkCode()" v-model="code" name="code"/>
                  <img src="static/img/code.bmp" alt=""/>
              </div>
          </div>
          <span class="errMess" v-text="codeErrorMsg">请输入正确的验证码</span>
      </div>
      <button class="btn">注册</button>
  </form>
  ```





### 06-解决服务器渲染被覆盖问题(掌握)

* 代码实现

  ```html
  //login.html
  <script>
  
      var vue = new Vue({
          el: "#app",
          data: {
              username: "",
              password: "",
              errorMsg: "[[${errorMsg}]]"
          },
      })
  
  </script>
  ```

  ```html
  //regist.html
  <script>
  
      var vue = new Vue({
          el: "#app",
          data: {
              username: "",//账户
              password: "",//密码
              repassword: "",//确认密码
              email: "",//邮箱
              code: "",//验证码
              usernameErrorMsg: "[[${errorMsg}]]",//账户的错误提示信息
              passwordErrorMsg: "",//密码的错误提示信息
              repasswordErrorMsg: "",//确认密码的错误提示信息
              emailErrorMsg: "",//邮箱的错误提示信息
              codeErrorMsg: ""
          },
  
      })
  
  
  </script>
  ```







### 07-进入后台管理页面(掌握)

* 需求

  * 继承ViewBaseServlet，从首页跳转到manager.html

* 代码实现

  ```java
  @WebServlet("/manager.html")
  public class ManagerServlet extends ViewBaseServlet {
  
      @Override
      protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
          processTemplate("pages/manager/manager", req, resp);
      }
  
      @Override
      protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
          doGet(req, resp);
      }
  }
  ```





### 08-图书管理系统的图书列表功能(掌握)

* 开发步骤

  * ①定义BookManagerServlet，继承于ModelBaseServlet
    * 获取图书列表，并转发到book_manager.html
  * ②编写book_manager.html

* ①定义BookManagerServlet，继承于ModelBaseServlet

  ```java
  @WebServlet("/protected/bookManager")
  public class BookManagerServlet extends ModelBaseServlet {
  
  
      /**
       * 转发到book_manager.html
       *
       * @param request
       * @param response
       */
      public void toBookManagerPage(HttpServletRequest request, HttpServletResponse response) {
          try {
              //获取所有图书列表
              BookService bookService = new BookServiceImpl();
              List<Book> bookList = bookService.selectBookList();
              request.setAttribute("bookList", bookList);
              //转发到book_manager.html
              processTemplate("pages/manager/book_manager", request, response);
          } catch (Exception e) {
              e.printStackTrace();
          }
      }
  
  
  }
  ```

* ②编写book_manager.html

  ```html
  <table>
      <thead>
      <tr>
          <th>图片</th>
          <th>商品名称</th>
          <th>价格</th>
          <th>作者</th>
          <th>销量</th>
          <th>库存</th>
          <th>操作</th>
      </tr>
      </thead>
      <tbody>
      <tr th:each="book:${bookList}">
          <td>
              <img th:src="${book.imgPath}" alt=""/>
          </td>
          <td th:text="${book.bookName}">活着</td>
          <td th:text="${book.price}">
              100.00
          </td>
          <td th:text="${book.author}">余华</td>
          <td th:text="${book.sales}">200</td>
          <td th:text="${book.stock}">400</td>
          <td>
              <a href="book_edit.html">修改</a><a href="" class="del">删除</a>
          </td>
      </tr>
  
      </tbody>
  </table>
  ```





### 09-图书管理系统的添加图书功能(掌握)

* 需求

  * 在book_manager.html页面上，点击添加图书功能 , 跳转到book_add.html页面, 并完成添加图书功能。

* 代码实现

  ```java
  @WebServlet("/protected/bookManager")
  public class BookManagerServlet extends ModelBaseServlet {
  
  
      /**
       * 转发到book_manager.html
       *
       * @param request
       * @param response
       */
      public void toBookManagerPage(HttpServletRequest request, HttpServletResponse response) {
          try {
              //获取所有图书列表
              BookService bookService = new BookServiceImpl();
              List<Book> bookList = bookService.selectBookList();
              request.setAttribute("bookList", bookList);
              //转发到book_manager.html
              processTemplate("pages/manager/book_manager", request, response);
          } catch (Exception e) {
              e.printStackTrace();
          }
      }
  
      /**
       * 转发到book_add.html
       *
       * @param request
       * @param response
       */
      public void toBookAddPage(HttpServletRequest request, HttpServletResponse response) {
          try {
              processTemplate("pages/manager/book_add", request, response);
          } catch (IOException e) {
              e.printStackTrace();
          }
      }
  
  
      /**
       * 添加图书
       *
       * @param request
       * @param response
       */
      public void addBook(HttpServletRequest request, HttpServletResponse response) {
          Book book = new Book();
          try {
              BeanUtils.populate(book, request.getParameterMap());
              BookService bookService = new BookServiceImpl();
              System.out.println(1 / 0);
              int addBook = bookService.addBook(book);
              if (addBook > 0) {
                  //添加图书成功 , 获取新的图书列表 ,跳转到book_manager.html (重定向)
                  response.sendRedirect(request.getContextPath() + "/protected/bookManager?method=toBookManagerPage");
              } else {
                  //添加图书失败 , 跳转回book_add.html
                  request.setAttribute("book", book);
                  toBookAddPage(request, response);
              }
          } catch (Exception e) {
              e.printStackTrace();
              //数据回显
              request.setAttribute("book", book);
              toBookAddPage(request, response);
  
          }
      }
  
  
  }
  ```

  ```html
  <form action="protected/bookManager">
      <input type="hidden" name="method" value="addBook">
      <div class="form-item">
          <div>
              <label>名称:</label>
              <input type="text" placeholder="请输入名称" name="bookName" th:if="${book!=null}" th:value="${book.bookName}"/>
              <input type="text" placeholder="请输入名称" name="bookName" th:if="${book==null}"/>
          </div>
          <span class="errMess" style="visibility: visible;">请输入正确的名称</span>
      </div>
      <div class="form-item">
          <div>
              <label>价格:</label>
              <input type="number" placeholder="请输入价格" name="price" th:if="${book!=null}" th:value="${book.price}"/>
              <input type="number" placeholder="请输入价格" name="price" th:if="${book==null}"/>
          </div>
          <span class="errMess">请输入正确数字</span>
      </div>
      <div class="form-item">
          <div>
              <label>作者:</label>
              <input type="text" placeholder="请输入作者" name="author" th:if="${book!=null}" th:value="${book.author}"/>
  
              <input type="text" placeholder="请输入作者" name="author" th:if="${book==null}"/>
          </div>
          <span class="errMess">请输入正确作者</span>
      </div>
      <div class="form-item">
          <div>
              <label>销量:</label>
              <input type="number" placeholder="请输入销量" name="sales" th:if="${book!=null}" th:value="${book.sales}"/>
              <input type="number" placeholder="请输入销量" name="sales" th:if="${book==null}"/>
          </div>
          <span class="errMess">请输入正确销量</span>
      </div>
      <div class="form-item">
          <div>
              <label>库存:</label>
              <input type="number" placeholder="请输入库存" name="stock" th:if="${book!=null}" th:value="${book.stock}"/>
              <input type="number" placeholder="请输入库存" name="stock" th:if="${book==null}"/>
          </div>
          <span class="errMess">请输入正确库存</span>
      </div>
  
      <button class="btn">提交</button>
  </form>
  ```





### 10-图书管理系统的删除图书功能(掌握)

* 需求

  * 在book_manager.html页面中，点击删除按钮，完成删除图书功能。

* 代码实现

  ```html
  <a th:href="@{/protected/bookManager(method=deleteBookById,bookId=${book.bookId})}" class="del">删除</a>
  ```

  ```java
  @WebServlet("/protected/bookManager")
  public class BookManagerServlet extends ModelBaseServlet {
      /**
       * 删除图书功能
       *
       * @param request
       * @param response
       */
      public void deleteBookById(HttpServletRequest request, HttpServletResponse response) {
  
          try {
              Integer bookId = Integer.parseInt(request.getParameter("bookId"));
              System.out.println("bookId = " + bookId);
              BookService bookService = new BookServiceImpl();
              int deleteBookById = bookService.deleteBookById(bookId);
          } catch (Exception e) {
              e.printStackTrace();
          }
  
          try {
              response.sendRedirect(request.getContextPath() + "/protected/bookManager?method=toBookManagerPage");
          } catch (IOException e) {
              e.printStackTrace();
          }
      }
  }
  ```





### 11-图书管理系统的修改图书功能(掌握)

* 需求

  * 在book_manager.html页面中，点击修改按钮，跳转到book_edit.html页面，并完成图书 修改功能。

* 代码实现

  ```java
  @WebServlet("/protected/bookManager")
  public class BookManagerServlet extends ModelBaseServlet {
  
      /**
       * 转发到book_edit.html
       *
       * @param request
       * @param response
       */
      public void toBookEditPage(HttpServletRequest request, HttpServletResponse response) {
          try {
              //方案一：只传递bookId，根据bookId查询数据库，获取book所有信息 (采用)
              //方案二：传递book所有信息
              Integer bookId = Integer.parseInt(request.getParameter("bookId"));
  
              BookService bookService = new BookServiceImpl();
              Book book = bookService.selectBookById(bookId);
              request.setAttribute("book", book);
              processTemplate("pages/manager/book_edit", request, response);
          } catch (Exception e) {
              e.printStackTrace();
          }
      }
  
  
      /**
       * 修改图书功能
       *
       * @param request
       * @param response
       */
      public void updateBookById(HttpServletRequest request, HttpServletResponse response) {
          Book book = new Book();
          try {
              BeanUtils.populate(book, request.getParameterMap());
              BookService bookService = new BookServiceImpl();
              int updateBookById = bookService.updateBookById(book);
              if (updateBookById > 0) {
                  //修改成功
                  response.sendRedirect(request.getContextPath() + "/protected/bookManager?method=toBookManagerPage");
              } else {
                  //修改失败
                  toBookEditPage(request, response);
              }
          } catch (Exception e) {
              e.printStackTrace();
              toBookEditPage(request, response);
  
          }
      }
  }
  ```