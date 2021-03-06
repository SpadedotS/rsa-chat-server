# rsa-chat-server
基于 websocket 的开源聊天室

特性
======
 * 使用websocket协议
 * 多浏览器支持（现代pc和移动端浏览器均支持）
 * 多房间支持
 * 掉线自动重连
 * 微博图片自动解析
 * 聊天内容支持微博表情
 * 聊天内容支持多种方式记录，文件、数据库
 * 支持多服务器部署
 
目标
======

聊天加密(使用aes加密，服务器不知道用户聊天内容)

支持rsa证书做用户注册机制

支持账号注册机制

聊天室的路径规则是:chat.abc.net:port/group-A

聊天室支持多种权限机制，开放、私有、密码等

图片上传或者外链图片

聊天记录(文本、数据库)

协议和代码开源支持多种实现以及自行搭建。


下载安装
=====
1、git clone https://github.com/chenjia404/rsa-chat-server

2、composer install

3、配置.env文件

启动停止(Linux系统)
=====
以debug方式启动  
```php start.php start  ```

以daemon方式启动  
```php start.php start -d ```

配置
=====
聊天内容日志位置,注意权限问题，程序尝试自动创建该目录。

CHAT_LOG_DIR=./logs/ 

聊天内容日志保存类型，file:文件,mysql:mysql数据库，如果是 file 需要配置 CHAT_LOG_DIR (聊天记录保存目录)，如果是 myslq 需要配置数据库相关。

CHAT_LOG_TYPE=file

更多参见.env.example

启动停止(Linux系统)
=====
以debug方式启动  
```php start.php start  ```

以daemon方式启动  
```php start.php start -d ```

TODO
=====
 * ~~贴图支持(新浪微博图片)~~
 * ~~微博表情~~
 * 上传图片支持(上传到本地)
 * ~~聊天记录(文本、数据库)~~
 * 房间密码功能
 * 聊天加密功能(aes加密，服务器不知道用户聊天内容)
 * rsa认证签名机制(无账号，但是可以在不同服务器保持同一身份)
 * 房间管理功能(禁言、屏蔽)


消息格式
======

消息格式为json,每种消息都有一个type字段区分类型。

客户端请求

| 类型 | 说明 | 举例 |
| --- | --- | --- |
| register | 登陆聊天室 | {"type":"register","username":"chenjia404","rsa_public_key":""} |
| login | 登陆聊天室 | {"type":"login","room_id":"123","username":"chenjia404","rsa_sign":""} |
| say | 发言 | {"type":"say","msg":"Hello, World!"} |
| logout | 退出聊天室 | {"type":"logout"} |

服务器响应

| 类型 | 说明 | 举例 |
| --- | --- | --- |
| reply_login | 响应登陆请求 | {"type":"reply_login ","code":0} |
| reply_say | 响应发言 | {"type":"reply_say ","code":1,"error_msg":"发布过于频繁"} |
| say | 发言推送 | {"type":"say ","msg_id":"423423452353252354","client_id":"0xi9090808","nickname":"chenjia404" ,"content":"Hello, World!","time":1508157282} |
| reply_logout | 响应退出聊天室 | {"type":"reply_login","code":0,"msg":"再见，欢迎下次再见～"} |

通用返回结构:
```
{
    "code":0,
    "msg":"成功登录",
    "data":{
        "type":"reply_login",
        "language":"zh-cn",
        "username":"chenjia404"
    }
}
```

聊天内容样例:
```
{
    "code":0,
    "msg":"成功登录",
    "data":
    {
        "type":"say",
        "msg_id":"423423452353252354",
        "username":"chenjia404",
        "content":"Hello, World!",
        "time":1508157282,
        "img":"https://tva3.sinaimg.cn/crop.19.12.155.155.180/659c6c35gw1f3swxjt6ooj2050050q30.jpg"
        "attachment":
        {
            "name":"rar.zip",
            "link":"https://cn.vuejs.org/v2/guide/class-and-style.html"
        }
    } 
}
```
code 字段为0则表示成功,非零 code 表示错误,每一个类型的错误使用唯一的错误码。

msg 字段为提示信息，需要对用户优化，可以直接提示给用户，支持多语言。

data 字段为实际返回字段，根据不同数据做不同的处理。

***错误码***

待定