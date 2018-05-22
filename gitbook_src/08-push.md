## 推送消息

### 参数列表

|   Variable          | Meanings  |
| :------------------ | :-----------------------------------|
|   $appId            |   小米开放平台申请的AppId             |
|   $appKey           |   小米开放平台申请的AppKey            |
|   $appSecret        |   小米开放平台申请的AppSecret         |
|   $fromAccount      |   表示消息发送方在APP帐号系统内唯一ID   |
|   $fromResource     |   表示消息发送方设备的标识             |
|   $toAccount        |   表示消息接收方在APP帐号系统内唯一ID   |
|   $toAccounts       |   表示消息接收方在APP帐号系统内唯一ID集合($toAccounts是一个List<String>类型的数据集合，例如[$account1,$account2])   |
|   $msgType          |   表示发送消息的类型<br />msgType="base64": msg是base64编码后的数据，一般传输二进制数据时使用;<br />msgType="": msg是原始数据，一般传输String数据时使用 |
|   $topicId          |   表示群ID                           |
|   $topicIds         |   表示群ID集合($topicIds是一个List<Long>类型的数据集合，例如[$topicId1,$topicId2])|
|   $packetId         |   表示发送消息包ID，由随机串+递增ID构成，在单个appAccount角度看可认为唯一 |

### 单用户推送单聊信息

+ HTTP 请求
```
curl https://mimc.chat.xiaomi.net/api/push/p2p/ -XPOST
  -d '{"appId":$appId, "appKey":$appKey，"appSecret":$appSecret, "fromAccount":$fromAccount, "fromResource":$fromResource, "toAccount":$toAccount, "msg":$msg, "msgType":$msgType}'
  -H "Content-Type: application/json"
```

+ JSON结果
```
{
    "code":200,
    "data":{"packetId":$packetId},
    "message":"success"
}
```

### 批量用户推送单聊信息
```
此功能目的是针对多个用户推送同一条消息。
```

+ HTTP 请求
```
curl https://mimc.chat.xiaomi.net/api/push/p2p/more -XPOST
  -d '{"appId":$appId, "appKey":$appKey，"appSecret":$appSecret, "fromAccount":$fromAccount, "fromResource":$fromResource, "toAccounts":$toAccounts, "msg":$msg, "msgType":$msgType}'
  -H "Content-Type: application/json"
```

##### 其中的$toAccounts变量格式为[$toAccount1,$toAccount2,...]，每一个用户名之间用逗号隔开。

+ JSON结果
```
{
    "code":200,
    "data":{
        "packetIds":[$packetId1,$packetId2]
    },
    "message":"success"
}
```

### 单群组推送群聊信息

+ HTTP 请求
```
curl https://mimc.chat.xiaomi.net/api/push/p2t/ -XPOST
  -d '{"appId":$appId, "appKey":$appKey，"appSecret":$appSecret, "fromAccount":$fromAccount, "fromResource":$fromResource, "msg":$msg, "topicId":$topicId, "msgType":$msgType}'
  -H "Content-Type: application/json"
```

+ JSON结果
```
{
    "code":200,
    "data":{"packetId":$packetId},
    "message":"success"
}
```

### 批量群组推送群聊信息
```
此功能目的是针对多个群组推送同一条消息。
```

+ HTTP 请求
```
curl https://mimc.chat.xiaomi.net/api/push/p2t/more -XPOST
  -d '{"appId":$appId, "appKey":$appKey，"appSecret":$appSecret, "fromAccount":$fromAccount, "fromResource":$fromResource, "topicIds":$topicIds, "msg":$msg, "msgType":$msgType}'
  -H "Content-Type: application/json"
```
##### 其中的$topicIds变量格式为[$topicId1,$topicId2,...]，每一个群ID之间用逗号隔开。

+ JSON结果
```
{
    "code":200,
    "data":{
        "packetIds":[$packetId1,$packetId2]
    },
    "message":"success"
}
```