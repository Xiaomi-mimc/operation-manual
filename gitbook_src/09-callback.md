## 消息回调
#### 应用场景
```
消息回调功能可以帮助应用方完全掌控App使用情况，回调消息数据可用于数据挖掘、统计、监控、App保活等。
```
#### 回调发送与失败重试
```
回调服务将App用户的即时消息和离线消息POST发给应用方，回调服务收到返回200状态码则表示接收成功
用户发送的完整消息体经base64编码后放置在payload字段中
当消息回调失败时，系统会重试最多3次（5s后，30s后，5min后）
```
#### 如何接入
```
管理平台：https://admin.mimc.chat.xiaomi.net
```

<div align="center"><img width="900" height="500" src="img-folder/msgcallback.png"/></div>

### 单聊即时消息回调
+ Post的body中JSON字符串结果
```
{
    "msgType":"NORMAL_MSG",
    "fromAppId":$fromAppId,
    "fromAccount":$fromAccount,
    "toAppId":$toAppId,
    "toAccount":$toAccount,
    "payload":$payload,
    "timestamp":$timestamp
}
```
### 单聊离线消息回调
+ Post的body中JSON字符串结果
```
{
    "msgType":"OFFLINE_MSG",
    "fromAppId":$fromAppId,
    "fromAccount":$fromAccount,
    "toAppId":$toAppId,
    "toAccount":$toAccount,
    "payload":$payload,
    "timestamp":$timestamp
}
```
### 群聊即时消息回调
+ Post的body中JSON字符串结果
```
{
    "msgType":"NORMAL_TOPIC_MSG",
    "appId":$appId, //发送目的群组的所属AppId
    "topicId":$topicId, //发送目的群组id
    "fromAccount":$fromAccount, //发送者用户名
    "toAccounts":[$toAccount1,$toAccount2,...,$toAccountN], //群组内所有用户
    "payload":$payload,
    "timestamp":$timestamp
}
```
### 群聊离线消息回调
+ Post的body中JSON字符串结果
```
{
    "msgType":"OFFLINE_TOPIC_MSG",
    "appId":$appId, //发送目的群组的所属AppId
    "topicId":$topicId, //发送目的群组id
    "fromAccount":$fromAccount, //发送者用户名
    "toAccounts":[$toAccount1,$toAccount3,...,$toAccountK], //群组内未收到消息的用户
    "payload":$payload,
    "timestamp":$timestamp
}
```