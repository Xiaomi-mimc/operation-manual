## 最近通讯列表
该功能应用于新设备登录后获取最近一个月的通讯列表，返回结果中包括最近联系的用户和群组信息，按时间降序排列。身份验证方式即客户端使用的token验证。

### 参数列表

|   Variable          | Meanings  |
| :------------------ | :-----------------------------------|
|   $appId            |   小米开放平台申请的AppId             |
|   $appKey           |   小米开放平台申请的AppKey            |
|   $appSecret        |   小米开放平台申请的AppSecret         |
|   $appAccount       |   查询方在APP帐号系统内唯一ID      |
|   $token            |   查询方的token（使用user.getToken()获取）       |
|   $member           |   与查询方进行会话的用户在APP帐号系统内唯一ID    |
|   $topicId          |   与查询方进行会话的群在APP帐号系统内唯一ID   |
|   $sequence         |   sequence主要用来做消息的排序和去重，全局唯一           |

### 获取最近通讯列表
+ HTTP 请求
```
curl "https://mimc.chat.xiaomi.net/api/contact/ -XGET
  -H "token:$token"
  -H "Content-Type: application/json"
```

+ JSON结果
```
{
    "code":200,
    "data":[
        {    "userType":"TOPIC",
            "id":"$topicId1",
            "name":"$topicName1",
            "timestamp":"$ts1",
            "lastMessage":{
                "fromUuid":"$fromUuid1",
                "fromAccount":"$fromAccount1",
                "payload":"$payload1",
                "sequence":"$sequence1"
            }
        }，
        {
            "userType":"TOPIC",
            "id":"$topicId2",
            "name":"$topicName2",
            "timestamp":"$ts2",
            "lastMessage":{
                "fromUuid":"$fromUuid2",
                "fromAccount":"$fromAccount2",
                "payload":"$payload2",
                "sequence":"$sequence2"
            }
        }，
        {
            "userType":"USER",
            "id":"$uuid1",
            "name":"$appAccount1",
            "timestamp":"$ts3",
            "lastMessage":{
                "fromUuid":"$fromUuid3",
                "fromAccount":"$fromAccount3",
                "payload":"$payload3",
                "sequence":"$sequence3"
            }
        }
    ],
    "message":"success"
}
```

### 删除指定单聊会话
+ HTTP 请求
```
curl "https://mimc.chat.xiaomi.net/api/contact/p2p/session?member=$member" -XDELETE
  -H "token:$token"
  -H "Content-Type: application/json"
```
+ JSON结果
```
同上，即与获取最近通讯列表的JSON结果一致。
```

### 删除指定群聊会话
+ HTTP 请求
```
curl "https://mimc.chat.xiaomi.net/api/contact/p2t/session?topicId=$topicId" -XDELETE
  -H "token:$token"
  -H "Content-Type: application/json"
```
+ JSON结果
```
同上，即与获取最近通讯列表的JSON结果一致。
```