## 在浏览器中输入URL之后发生了神马？

### 浏览器中输入网址
  1、URL的组成：协议 + 域名 + 端口 + 路径 + 文件 + 参数<br>

### 域名系统解析
  1、通过DNS解析域名的实际IP地址<br>
  2、过程（递归查询）： 浏览器缓存查询---> OS(操作系统)中查询 ---> 路由器中查询 ---> ISP中查询

### 浏览器缓存
  1、通过Cache-control和Expires判断是否命中强缓存，如果命中，直接拿数据展示
  2、如果未命中强缓存，则发请求到服务器（建立TCP连接），服务器通过Etag和Last-Modify来判断是否命中协商缓存，如果没有更改，则返回304，浏览器读取本地缓存
  3、如果强缓存和协商缓存都没有命中，则返回请求结果<br>

### 建立TCP连接（3次握手）
  1、客户端向服务器端发送带SYN标识的消息，确定是否可以开始连接，此时客户端的状态变为SYN_SEND<br><br>
  2、服务端收到消息之后，返回给客户端带有SYN和ACK标识的信息给客户端，此时服务器端的状态变为SYN_RECIVE<br><br>
  3、客户端收到消息之后，在给服务器端发送一个带ACK标识的信息，此时客户端和服务器端的状态都变为ESTABLISHED状态<br><br>
  4、此时连接完成，可发送数据<br><br>
  【为什么要进行3次握手】：为了保证客户端发来的请求是本次通信的客户端

### 对https协议进行加密处理
### 浏览器请求页面html
### 服务器响应html
### 浏览器解析html
### 浏览器渲染html
### 浏览器解析js脚本
### 浏览器发起网络请求
### 服务器响应ajax请求
### 浏览器处理时间循环等异步逻辑