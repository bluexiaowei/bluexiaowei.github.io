# HTTP（超文本传输​​协议）


http 请求（Request）：

* Method (方法)
* Path (路径)
* Version of the protocol (协议版本)
* Headers (请求头)
* Body (请求体)

```Text
GET / HTTP/1.1
Host: developer.mozilla.org
Accept-Language: fr
```

http 返回 (Response):

* Version of the protocol (协议版本)
* Status code (状态码)
* Status message (状态信息)
* Headers (返回头)
* Body (返回体)

```Text
HTTP/1.1 200 OK
Date: Sat, 09 Oct 2010 14:28:02 GMT
...
Content-Length: 29769
Content-Type: text/html
```

## Cache（http 缓存）

> 重用已获取的资源能够有效的提升网站与应用的性能。Web 缓存能够减少延迟与网络阻塞，进而减少显示某个资源所用的时间。借助 HTTP 缓存，Web 站点变得更具有响应性。

### `Cache-Control`（控制浏览器缓存）

* no-store（禁止缓存）
* private（不被中间人缓存）
* public（可以被任何中间人缓存）
* max-age=seconds（seconds 缓存获取时间 秒）
* must-revalidate（验证是否过期）

缓存失效时间 `expirationTime = responseTime + freshnessLifetime - currentAge`

问题：缓存时间设置过长，新资源无法得到更新。

解决方案：对资源添加版本信息或者指纹标签。

专业方案：Steve Souders 称之为 [revving](https://www.stevesouders.com/blog/2008/08/23/revving-filenames-dont-use-querystring/) 的技术

## Cookie

> 是服务器发送到用户浏览器并保存在本地的一小块数据，它会在浏览器下次向同一服务器再发起请求时被携带并发送到服务器上。
>
> 主要作用：会话状态管理、个性化设置和浏览器行为设置。

### `Set-Cookie`（设置 Cookie）

* cookieName=cookieValue（设置键值对）
* Expires=WEb, Date（设置过期时间）
* Domain=xxxx.com（指定使用域名，如果不指定为当前主机）
* Path=/XXX（指定主机路径接收 Cookie）
* Secure（只应通过被HTTPS协议加密过的请求发送给服务端）
* HttpOnly（`Document.cookie`API无法访问带有 HttpOnly 标记的Cookie）

```Text
Set-Cookie: id=a3fWa; Expires=Wed, 21 Oct 2015 07:28:00 GMT; Domain=mozilla.org; Secure; HttpOnly;
```

## CORS（跨域资源共享）

> 当一个资源从与该资源本身所在的服务器**不同的域、协议或端口**请求一个资源时，资源会发起一个跨域 HTTP 请求。
>
> **出于安全原因**，浏览器限制从脚本内发起的跨源HTTP请求。并不一定是浏览器限制了发起跨站请求，也可能是跨站请求可以正常发起，但是返回结果被浏览器拦截了。

## 相关资料
