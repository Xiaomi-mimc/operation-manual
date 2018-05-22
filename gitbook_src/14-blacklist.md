## 单聊黑名单
```
该功能限于单聊的黑名单功能：
    A拉黑B，B不能给A发送消息，A可以继续给B发送消息。
```
### 参数列表

| Variable | Meanings |
| :------------------ | :-----------------------------------|
| $appId | 小米开放平台申请的AppId |
| $appKey | 小米开放平台申请的AppKey |
| $appSecret | 小米开放平台申请的AppSecret |
| $appAccount       | 表示查询方在APP帐号系统内唯一ID       |
| $token           | 查询方的token（使用user.getToken()获取）                   |
| $blackAccount     | 表示被拉黑方在APP帐号系统内唯一ID       |

### 拉黑

+ HTTP请求

```
curl https://mimc.chat.xiaomi.net/api/blacklist/ -XPOST
  -d '{"blackAccount":"$blackAccount"}'
  -H "appId:$appId"
  -H "appKey:$appKey"
  -H "appSecret:$appSecret"
  -H "appAccount:$appAccount"
  -H "Content-Type: application/json"

curl https://mimc.chat.xiaomi.net/api/blacklist/ -XPOST
  -d '{"blackAccount":"$blackAccount"}'
  -H "token:$token"
  -H "Content-Type: application/json"
```

+ JSON结果

```
{
    "code":200,
    "message":"success",
    "data":null,
}
```

### 取消拉黑

+ HTTP 请求

```
curl https://mimc.chat.xiaomi.net/api/blacklist/?blackAccount=$blackAccount -XDELETE
  -H "appId:$appId"
  -H "appKey:$appKey"
  -H "appSecret:$appSecret"
  -H "appAccount:$appAccount"
  -H "Content-Type: application/json"

curl https://mimc.chat.xiaomi.net/api/blacklist/?blackAccount=$blackAccount -XDELETE
  -H "token:$token"
  -H "Content-Type: application/json"
```

+ JSON结果

```
{
    "code":200,
    "message":"success",
    "data":null,
}
```

### 是否拉黑

+ HTTP 请求

```
curl https://mimc.chat.xiaomi.net/api/blacklist/?blackAccount=$blackAccount -XGET
  -H "appId:$appId"
  -H "appKey:$appKey"
  -H "appSecret:$appSecret"
  -H "appAccount:$appAccount"
  -H "Content-Type: application/json"

curl https://mimc.chat.xiaomi.net/api/blacklist/?blackAccount=$blackAccount -XGET
  -H "token:$token"
  -H "Content-Type: application/json"
```

+ JSON结果

```
{
    "code":200,
    "message":"success",
    "data":{
        "isBlack":true/false
    },
}
```