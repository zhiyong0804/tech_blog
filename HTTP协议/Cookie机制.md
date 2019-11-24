# Cookie机制
#### 工作流程
* request Cookie
* response Set-Cookie

#### 生存周期
* “Expires”俗称“过期时间”，用的是绝对时间点，可以理解为“截止日期”（deadline）。
* “Max-Age”用的是相对时间，单位是秒，浏览器用收到报文的时间点再加上 Max-Age，就可以得到失效的绝对时间。

#### 作用域
* 让浏览器仅发送给特定的服务器和 URI，避免被其他网站盗用
* Domain，HTTPOnly

#### 基本用途
* 身份识别，实现有状态的会话事务
* Cookie设置为0，服务器0秒就让Cookie失效，即立即失效，服务器不存Cookie。