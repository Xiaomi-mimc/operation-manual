## 群组管理

### 参数列表

|   Variable   | Meanings  |
| :------------------ | :--------------------------------------------------------|
|   $appId            |   小米开放平台申请的AppId                                  |
|   $appKey           |   小米开放平台申请的AppKey                                 |
|   $appSecret        |   小米开放平台申请的AppSecret                              |
|   $topicId          |   表示群ID                                                |
|   $topicName        |   表示创建群的时候所指定的群名称                            |
|   $topicId1         |   表示查询所属群信息时用户所加入群的群ID                       |
|   $topicId2         |   表示查询所属群信息时用户所加入群的群ID                     |
|   $topicName1       |   表示查询所属群信息时用户所加入群的群名称                    |
|   $topicName2       |   表示查询所属群信息时用户所加入群的群名称                      |
|   $topicBulletin1   |      表示查询所属群信息时用户所加入群的群公告                    |
|   $topicBulletin2   |   表示查询所属群信息时用户所加入群的群公告                    |
|   $newBulletin      |      表示更新群时设置的新群公告                                |
|   $newTopicName     |   表示更新群时设置的新群名称                                |
|   $ownerUuid          |      表示群主在MIMC帐号系统内uuid(使用user.getUuid()获取)      |
|   $ownerAccount     |      表示群主在APP帐号系统内唯一ID                             |
|   $ownerToken          |      表示群主token（使用user.getToken()获取）                 |
|   $userAccount1     |      表示群成员1号在APP帐号系统内唯一ID                        |
|   $userAccount2     |      表示群成员2号在APP帐号系统内唯一ID                        |
|   $userAccount3     |   表示群成员3号在APP帐号系统内唯一ID                        |
|   $userAccount4     |      表示群成员4号在APP帐号系统内唯一ID                        |
|   $userAccount5     |      表示群成员5号在APP帐号系统内唯一ID                        |
|   $userUuid1          |      表示userAccount1在MIMC帐号系统内uuid(使用user.getUuid()获取)|
|   $userToken1          |      表示userAccount1的token（使用user.getToken()获取）      |


#### 备注：
```
token的获取使用User.getToken()方法。
uuid的获取使用User.getUuid()方法，uuid由MIMC根据($appId, $appAccount)生成，全局唯一。
身份认证有两种方式：1. token（$ownerToken/$userToken1）; 2. app信息,app帐号（$appKey，$appSecret，$ownerAccount/$userAccount1）。
当两种认证信息都存在时，优先验证前者。前者一般用于app客户端，后者一般用于app服务端。下面给出了这两种的使用方式。
```


### 人数上限
```
当前群默认限制500人
```

### 创建群

#### 如下为$ownerAccount创建群

+ HTTPS请求

```
curl "https://mimc.chat.xiaomi.net/api/topic/$appId" -XPOST
  -d '{"topicName":$topicName,"accounts":"$userAccount1,$userAccount2,$userAccount3"}'
  -H "Content-Type: application/json"
  -H "token:$ownerToken"

curl "https://mimc.chat.xiaomi.net/api/topic/$appId" -XPOST
  -d '{"topicName":$topicName,"accounts":"$userAccount1,$userAccount2,$userAccount3"}'
  -H "Content-Type: application/json"
  -H "appKey:$appKey"
  -H "appSecret:$appSecret"
  -H "appAccount:$ownerAccount"
```

+ JSON结果

```
{
     "code":200,"message":"success",
     "data":{
        "topicInfo":{
            "topicId":$topicId,
            "ownerUuid":$ownerUuid,
            "ownerAccount":$ownerAccount,
            "topicName":$topicName,
            "bulletin":""
        },
        "members":[
            {"uuid":$ownerUuid,"account":$ownerAccount},
            {"uuid":$userUuid1,"account":$userAccount1},
            {"uuid":$userUuid2,"account":$userAccount2},
            {"uuid":$userUuid3,"account":$userAccount3}
        ]
    }
}
```


### 查询指定群信息

#### 如下为$userAccount1查询群信息

+ HTTPS请求

```
curl "https://mimc.chat.xiaomi.net/api/topic/$appId/$topicId" -XGET
  -H "Content-Type: application/json"
  -H "token:$userToken1"

curl "https://mimc.chat.xiaomi.net/api/topic/$appId/$topicId" -XGET
  -H "Content-Type: application/json"
  -H "appKey:$appKey"
  -H "appSecret:$appSecret"
  -H "appAccount:$userAccount1"
```

+ JSON结果

```
{
     "code":200,"message":"success",
     "data":{
        "topicInfo":{
            "topicId":$topicId,
            "ownerUuid":$ownerUuid,
            "ownerAccount":$ownerAccount,
            "topicName":$topicName,
            "bulletin":""
        },
        "members":[
            {"uuid":$ownerUuid,"account":$ownerAccount},
            {"uuid":$userUuid1,"account":$userAccount1},
            {"uuid":$userUuid2,"account":$userAccount2},
            {"uuid":$userUuid3,"account":$userAccount3}
        ]
     }
}
```

### 查询所属群信息

#### 如下为$userAccount1查询加入的所有群信息

+ HTTPS请求

```
curl "https://mimc.chat.xiaomi.net/api/topic/$appId/account" -XGET
  -H "Content-Type: application/json"
  -H "token:$userToken1"

curl "https://mimc.chat.xiaomi.net/api/topic/$appId/account" -XGET
  -H "Content-Type: application/json"
  -H "appKey:$appKey"
  -H "appSecret:$appSecret"
  -H "appAccount:$userAccount1"
```

+ JSON结果

```
{
    "code":200,
    "message":"success",
    "data":[
        {
            "topicId":$topicId1,
            "ownerUuid":$ownerUuid,
            "ownerAccount":$ownerAccount,
            "topicName":$topicName1,
            "bulletin":$topicBulletin1
        },
        {
            "topicId":$topicId2,
            "ownerUuid":$ownerUuid,
            "ownerAccount":$ownerAccount,
            "topicName":$topicName2,
            "bulletin":$topicBulletin2
        }
    ]
}
```

### 邀请用户加入群

#### 如下为$userAccount1邀请$userAccount4,$userAccount5加入群

+ HTTPS请求

```
curl "https://mimc.chat.xiaomi.net/api/topic/$appId/$topicId/accounts" -XPOST
  -d '{"accounts":"$userAccount4,$userAccount5"}'
  -H "Content-Type: application/json"
  -H "token:$userToken1"

curl "https://mimc.chat.xiaomi.net/api/topic/$appId/$topicId/accounts" -XPOST
  -d '{"accounts":"$userAccount4,$userAccount5"}'
  -H "Content-Type: application/json"
  -H "appKey:$appKey"
  -H "appSecret:$appSecret"
  -H "appAccount:$userAccount1"
```

+ JSON结果

```
{
     "code":200,"message":"success",
     "data":{
        "topicInfo":{
            "topicId":$topicId,
            "ownerUuid":$ownerUuid,
            "ownerAccount":$ownerAccount,
            "topicName":$topicName,
            "bulletin":""
        },
        "members":[
            {"uuid":$ownerUuid,"account":$ownerAccount},
            {"uuid":$userUuid1,"account":$userAccount1},
            {"uuid":$userUuid2,"account":$userAccount2},
            {"uuid":$userUuid3,"account":$userAccount3},
            {"uuid":$userUuid4,"account":$userAccount4},
            {"uuid":$userUuid5,"account":$userAccount5}
        ]
    }
}
```

### 非群主用户退群

#### 如下为$userAccount1退群

+ HTTPS请求

```
curl "https://mimc.chat.xiaomi.net/api/topic/$appId/$topicId/account" -XDELETE
  -H "Content-Type: application/json"
  -H "token:$userToken1"

curl "https://mimc.chat.xiaomi.net/api/topic/$appId/$topicId/account" -XDELETE
  -H "Content-Type: application/json"
  -H "appKey:$appKey"
  -H "appSecret:$appSecret"
  -H "appAccount:$userAccount1"
```

+ JSON结果

```
{"code":200,"message":"success","data":null}
```

+ 若是群主退群，则JSON结果如下：

```
{"code":500,"message":"quit topic fail","data":null}
```

### 群主踢用户退群

#### 如下为$ownerAccount踢$userAccount4,$userAccount5退出群

+ HTTPS请求

```
curl "https://mimc.chat.xiaomi.net/api/topic/$appId/$topicId/accounts?accounts=$userAccount4,$userAccount5" -XDELETE
  -H "Content-Type: application/json"
  -H "token:$ownerToken"

curl "https://mimc.chat.xiaomi.net/api/topic/$appId/$topicId/accounts?accounts=$userAccount4,$userAccount5" -XDELETE
  -H "Content-Type: application/json"
  -H "appKey:$appKey"
  -H "appSecret:$appSecret"
  -H "appAccount:$ownerAccount"
```

+ JSON结果

```
{
     "code":200,"message":"success",
     "data":{
        "topicInfo":{
            "topicId":$topicId,
            "ownerUuid":$ownerUuid,
            "ownerAccount":$ownerAccount,
            "topicName":$topicName,
            "bulletin":""
        },
        "members":[
            {"uuid":$ownerUuid,"account":$ownerAccount},
            {"uuid":$userUuid2,"account":$userAccount2},
            {"uuid":$userUuid3,"account":$userAccount3}
        ]
    }
}
```

### 群主更新群信息

#### 如下为$ownerAccount更新群信息：群主为$userAccount2，群名称为$newTopicName，群公告为$newBulletin,也可只对某一项修改，如群名称，其它项不传数据即可。

+ HTTPS请求

```
curl "https://mimc.chat.xiaomi.net/api/topic/$appId/$topicId" -XPUT
  -d '{"ownerAccount":$userAccount2,"topicName":$newTopicName,"bulletin":$newBulletin}'
  -H "Content-Type: application/json"
  -H "token:$ownerToken"

curl "https://mimc.chat.xiaomi.net/api/topic/$appId/$topicId" -XPUT
  -d '{"ownerAccount":$userAccount2,"topicName":$newTopicName,"bulletin":$newBulletin}'
  -H "Content-Type: application/json"
  -H "appKey:$appKey"
  -H "appSecret:$appSecret"
  -H "appAccount:$ownerAccount"
```

+ JSON结果

```
{
     "code":200,"message":"success",
     "data":{
        "topicInfo":{
            "topicId":$topicId,
            "ownerUuid":$userUuid2,
            "ownerAccount":$userAccount2,
            "topicName":$newTopicName,
            "bulletin":$newBulletin
        },
        "members":[
            {"uuid":$ownerUuid,"account":$ownerAccount},
            {"uuid":$userUuid2,"account":$userAccount2},
            {"uuid":$userUuid3,"account":$userAccount3}
        ]
    }
}
```

### 群主销毁群

#### 如下为群主销毁群

+ HTTPS请求

```
curl "https://mimc.chat.xiaomi.net/api/topic/$appId/$topicId" -XDELETE
  -H "Content-Type: application/json"
  -H "token:$ownerToken"

curl "https://mimc.chat.xiaomi.net/api/topic/$appId/$topicId" -XDELETE
  -H "Content-Type: application/json"
  -H "appKey:$appKey"
  -H "appSecret:$appSecret"
  -H "appAccount:$ownerAccount"
```

+ JSON结果

```
{"code":200,"message":"success！","data":null}
```