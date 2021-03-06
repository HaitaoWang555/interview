## HTTP 状态码知道哪些？分别什么意思？

  **2XX 成功**

  - 200 OK，表示从客户端发来的请求在服务器端被正确处理
  - 204 No content，表示请求成功，但响应报文不含实体的主体部分
  - 205 Reset Content，表示请求成功，但响应报文不含实体的主体部分，但是与 204 响应不同在于要求请求方重置内容
  - 206 Partial Content，进行范围请求

  **3XX 重定向**

  - 301 moved permanently，永久性重定向，表示资源已被分配了新的 URL
  - 302 found，临时性重定向，表示资源临时被分配了新的 URL
  - 303 see other，表示资源存在着另一个 URL，应使用 GET 方法获取资源
  - 304 not modified，表示服务器允许访问资源，但因发生请求未满足条件的情况
  - 307 temporary redirect，临时重定向，和302含义类似，但是期望客户端保持请求方法不变向新的地址发出请求

  **4XX 客户端错误**

  - 400 bad request，请求报文存在语法错误
  - 401 unauthorized，表示发送的请求需要有通过 HTTP 认证的认证信息
  - 403 forbidden，表示对请求资源的访问被服务器拒绝
  - 404 not found，表示在服务器上没有找到请求的资源

  **5XX 服务器错误**

  - 500 internal sever error，表示服务器端在执行请求时发生了错误
  - 501 Not Implemented，表示服务器不支持当前请求所需要的某个功能
  - 503 service unavailable，表明服务器暂时处于超负载或正在停机维护，无法处理请求

## Post 和 Get 的区别

  - Get 请求能缓存，Post 不能
  - Post 相对 Get 安全一点点，因为Get 请求都包含在 URL 里，且会被浏览器保存历史纪录，Post 不会，但是在抓包的情况下都是一样的。
  - Post 可以通过 request body来传输比 Get 更多的数据，Get 没有这个技术
  - URL有长度限制，会影响 Get 请求，但是这个长度限制是浏览器规定的，不是 RFC 规定的
  - Post 支持更多的编码类型且不对数据类型限制

## HTTP 缓存有哪几种？
  - 强缓存 可以通过两种响应头实现：`Expires` 和 `Cache-Control` 。强缓存表示在缓存期间不需要请求，`state code` 为 200
  - 协商缓存 协商缓存需要请求，如果缓存有效会返回 304
  `Last-Modified` 和 `If-Modified-Since`
  `Last-Modified` 表示本地文件最后修改日期，`If-Modified-Since` 会将 `Last-Modified` 的值发送给服务器，询问服务器在该日期后资源是否有更新，有更新的话就会将新的资源发送回来。

  但是如果在本地打开缓存文件，就会造成 `Last-Modified` 被修改，所以在 HTTP / 1.1 出现了 `ETag` 。

  `ETa`g 和 `If-None-Match`
  `ETag` 类似于文件指纹，`If-None-Match` 会将当前 `ETag` 发送给服务器，询问该资源 `ETag` 是否变动，有变动的话就将新的资源发送回来。并且 `ETag` 优先级比 `Last-Modified` 高。

- HTTPS

## HTTP 2.0

  - 二进制传输 

    HTTP 2.0 中所有加强性能的核心点在于此。在之前的 HTTP 版本中，我们是通过文本的方式传输数据。在 HTTP 2.0 中引入了新的编码机制，所有传输的数据都会被分割，并采用二进制格式编码。

  - 多路复用

    在 HTTP 2.0 中，有两个非常重要的概念，分别是帧（frame）和流（stream）。

    帧代表着最小的数据单位，每个帧会标识出该帧属于哪个流，流也就是多个帧组成的数据流。

    多路复用，就是在一个 TCP 连接中可以存在多条流。换句话说，也就是可以发送多个请求，对端可以通过帧中的标识知道属于哪个请求。通过这个技术，可以避免 HTTP 旧版本中的队头阻塞问题，极大的提高传输性能。    

  - Header 压缩

    在 HTTP 1.X 中，我们使用文本的形式传输 header，在 header 携带 cookie 的情况下，可能每次都需要重复传输几百到几千的字节。

    在 HTTP 2.0 中，使用了 HPACK 压缩格式对传输的 header 进行编码，减少了 header 的大小。并在两端维护了索引表，用于记录出现过的 header ，后面在传输过程中就可以传输已经记录过的 header 的键名，对端收到数据后就可以通过键名找到对应的值。

  - 服务端 Push

    在 HTTP 2.0 中，服务端可以在客户端某个请求后，主动推送其他资源。

    可以想象以下情况，某些资源客户端是一定会请求的，这时就可以采取服务端 push 的技术，提前给客户端推送必要的资源，这样就可以相对减少一点延迟时间。当然在浏览器兼容的情况下你也可以使用 prefetch 。

## DNS
  DNS 的作用就是通过域名查询到具体的 IP。

## 从输入 URL 到页面加载完成的过程

- 首先做 DNS 查询，如果这一步做了智能 DNS 解析的话，会提供访问速度最快的 IP 地址回来
- 接下来是 TCP 握手 TLS 握手，然后就开始正式的传输数据
- 首先浏览器会判断状态码是什么，如果是 200 那就继续解析，如果 400 或 500 的话就会报错，如果 300 的话会进行重定向，这里会有个重定向计数器，避免过多次的重定向，超过次数也会报错
- 浏览器开始解析文件，如果是 gzip 格式的话会先解压一下，然后通过文件的编码格式知道该如何去解码文件
- 文件解码成功后会正式开始渲染流程，先会根据 HTML 构建 DOM 树，有 CSS 的话会去构建 CSSOM 树。如果遇到 `script` 标签的话，会判断是否存在 `async` 或者 `defer` ，前者会并行进行下载并执行 JS，后者会先下载文件，然后等待 HTML 解析完成后顺序执行，如果以上都没有，就会阻塞住渲染流程直到 JS 执行完毕。遇到文件下载的会去下载文件，这里如果使用 HTTP 2.0 协议的话会极大的提高多图的下载效率。
- 初始的 HTML 被完全加载和解析后会触发 `DOMContentLoaded` 事件
- CSSOM 树和 DOM 树构建完成后会开始生成 Render 树，这一步就是确定页面元素的布局、样式等等诸多方面的东西
- 在生成 Render 树的过程中，浏览器就开始调用 GPU 绘制，合成图层，将内容显示在屏幕上了
