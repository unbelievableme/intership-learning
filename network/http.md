### HTTP报文
- 请求头  
    - 请求行  
    - 请求头  

- 空行  

- 请求正文  



### Request URL

### Request Method  
- GET

- POST   

- DELETE  

- PUT  

- PATCH  

- OPTIONS  


### Status Code  
- 3xx
    - 301
    - 302 
    - 303
    - 304
    - 307
- 4xx  
    - 400
    - 401 
    - 403 
    - 404 

- 5xx
    - 500 
    - 501 
    - 502 
    - 503 
    - 504 

## General Headers
- Cache-Control 
    - no-store
    - no-cache
    - public
    - private
    - max-age
- Connection

- Transfer-Encoding

- Date


## Request Headers
- Accept

- Cookie  
    - 机制  
    - 用途/作用
    - 创建过程  
    - 有效期(Expire)  
    - 作用域(Domian,Path)  
    - 禁用cookie
    - cookie与session  

- if-Modified-Since

- If-None-Match

- Host 

- User-Agent  



## Response Headers
- Set-Cookie  

- Location  

- Server   

- Status  

- Etag

- Last-Modified 

## HTTP版本  
- http1.0与1.1区别
    - 长连接  
    - 100状态码 
    - Host域
- http1.1与1.2区别  
    - 二进制分帧  
    - [服务器推送](http://www.ruanyifeng.com/blog/2018/03/http2_server_push.html)
    - [头部压缩](https://www.jianshu.com/p/f44b930cfcac)  

## web安全 
- CSRF(跨站请求伪造) 
    - 原理
    - 解决方法  
- DDOS

- XSS
    - 存储式
    - 反射式
- SQL注入