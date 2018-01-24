# operation-manual


## 目录
* [快速开始](#快速开始)
    * [Android](https://github.com/Xiaomi-mimc/mimc-android-sdk)
    * [Web](https://github.com/Xiaomi-mimc/mimc-webjs-sdk)
    * [iOS](https://github.com/Xiaomi-mimc/mimc-ios-sdk)
    * [PC](https://github.com/Xiaomi-mimc/mimc-java-sdk)
* [获取token](#获取token)
* [推荐消息格式](#推荐消息格式)
    * [普通类型消息](#普通类型消息)
    * [图片文件等类型消息](#图片文件等类型消息)

* [推送消息](#推送消息)
    * [推送单聊信息](#推送单聊信息)
    * [推送群聊信息](#推送群聊信息)

* [群聊消息](#群聊消息)
    * [创建群](#创建群)
    * [查询指定群信息](#查询指定群信息)
    * [查询所属群信息](#查询所属群信息)
    * [邀请用户加入群](#邀请用户加入群)
    * [非群主用户退群](#非群主用户退群)
    * [群主踢用户退群](#群主踢用户退群)
    * [群主更新群信息](#群主更新群信息)
    * [群主销毁群](#群主销毁群)
* [消息漫游](#消息漫游)
     * [拉取单聊消息记录](#拉取单聊消息记录)
     * [拉取群聊消息记录](#拉取群聊消息记录)
* [联系我们](#联系我们)

## 快速开始

#### 1）[Android](https://github.com/Xiaomi-mimc/mimc-android-sdk)

#### 2）[Web](https://github.com/Xiaomi-mimc/mimc-webjs-sdk)

#### 3）[iOS](https://github.com/Xiaomi-mimc/mimc-ios-sdk)

#### 4）[PC](https://github.com/Xiaomi-mimc/mimc-java-sdk)


## 获取Token

```
APP客户端获取Token的逻辑如下：
    APP <--> AppProxyService(APP开发者实现) <--> 小米TokenService(MIMC)

APP应用方实现AppProxyService服务获取Token，该服务需实现以下功能：
    1. 存储appId/appKey/appSec（不应存储在客户端，防止泄露）
    2. 用户在APP系统内的合法鉴权
    3. 调用小米TokenService服务，并将小米TokenService服务返回结果返回客户端

AppProxyService访问小米TokenService的方式如下：
```

### 参数列表

|   Variable          | Meanings  |
| :------------------ | :------------------------------------|
|   $appId            |   小米开放平台申请的AppId              |
|   $appKey           |   小米开放平台申请的AppKey             |
|   $appSecret        |   小米开放平台申请的AppSecret	      |
|   $appPackage       |   小米开放平台申请的appPackage         |
|   $appAccount       |   表示消息发送方成员号account(app账号)  |
|   $chid             |   表示服务的标识			|
|   $uuid             |   表示用户的userId			  |
|   $token	      |   表示当前$appAccount的token 	  |
|   $packetId         |   表示发送消息包ID                     |

+ HTTP 请求
```
curl "https://mimc.chat.xiaomi.net/api/account/token" -XPOST -d '{"appId":$appId,"appKey":$appKey,"appSecret":$appSecret,"appAccount":$appAccount}' -H "Content-Type: application/json"
```

+ JSON结果
```
{
	"code": 200,
	"message": "success",
	"data": {
		"appId": $appId,
		"appPackage": $appPackage,
		"appAccount": $appAccount,
		"miChid": $chid,
		"miUserId": $uuid,
		"miUserSecurityKey": $appSecret,
		"token": $token
	}
}
```

## 推荐消息格式

#### MIMC的消息数据APP开发者100%自定义

#### 普通类型消息
    
```
1.将消息体通过MIMC发送给对端
2.对端接收并展现
建议格式：
	{
	    version: 0, // 建议保留version字段，方便后续协议升级兼容
	    msgId: "12345",
	    msgType: "TEXT", // TEXT|PIC_FILE|AUDIO_FILE|BIN_FILE
	    timestamp: "1516763973000", // 建议精确到毫秒
	    payload: "欢迎使用小米即时消息云(MIMC)",
	}
```
    
#### 图片文件等类型消息
```
1.将图片文件/语音文件/视频文件（非实时语音视频聊天）等上传到服务器，获得一个URL
2.通过MIMC将URL发送给对端
3.对端通过URL下载图片文件/语音文件/视频文件等进行展示
建议格式：
    {
        version: 0, // 建议保留version字段，方便后续协议升级兼容
        msgId: "12345",
        msgType: "PIC_FILE", // TEXT|PIC_FILE|AUDIO_FILE|BIN_FILE
        timestamp: "1516763973000", // 建议精确到毫秒
        payload: "https://github.com/Xiaomi-mimc/operation-manual/blob/master/img-folder/%E6%88%91%E7%B1%B3%E5%8D%B3%E6%97%B6%E6%B6%88%E6%81%AF%E4%BA%91.jpg",
    }
```

## 推送消息

### 参数列表

|   Variable          | Meanings  |
| :------------------ | :------------------------------------|
|   $appId            |   小米开放平台申请的AppId              |
|   $appKey           |   小米开放平台申请的AppKey             |
|   $appSecret        |   小米开放平台申请的AppSecret	      |
|   $fromAccount      |   表示消息发送方成员号account(app账号)  |
|   $fromResource     |   表示用户设备的标识                   |
|   $toAccount        |   表示消息接收方成员号account(app账号)  |
|   $msgType          |   表示发送消息的类型(msgType="base64": msg是base64编码后的数据，一般传输二进制数据时使用;msgType="":msg是原始数据，一般传输String数据时使用)  |
|   $topicId	      |   表示群ID                            |
|   $packetId         |   表示发送消息包ID                     |


### 推送单聊信息

+ HTTP 请求
```
curl https://mimc.chat.xiaomi.net/api/push/p2p/ -XPOST -d '{"appId":$appId, "appKey":$appKey，"appSecret":$appSecret, "fromAccount":$fromAccount, "fromResource":$fromResource, "toAccount":$toAccount, "msg":$msg, "msgType":$msgType}' -H "Content-Type: application/json"
```

+ JSON结果
```
{
	"code":200,
	"data":{"packetId":$packetId},
	"message":"success"
}
```

### 推送群聊信息

+ HTTP 请求
```
curl https://mimc.chat.xiaomi.net/api/push/p2t/ -XPOST -d '{"appId":$appId, "appKey":$appKey，"appSecret":$appSecret, "fromAccount":$fromAccount, "fromResource":$fromResource, "msg":$msg, "topicId":$topicId, "msgType":$msgType}' -H "Content-Type: application/json"
```

+ JSON结果
```
{
	"code":200,
	"data":{"packetId":$packetId},
	"message":"success"
}
```

## 群聊消息

### 参数列表

|   Variable   | Meanings  |
| :------------------ | :--------------------------------------------------------|
|   $appId            |   小米开放平台申请的AppId                                  |
|   $appKey           |   小米开放平台申请的AppKey                                 |
|   $appSecret        |   小米开放平台申请的AppSecret	                            |
|   $topicId          |   表示群ID                                                |
|   $topicName        |   表示创建群的时候所指定的群名称                            |
|   $topicId1         |   表示查询所属群信息时用户所加入群的群ID	                   |
|   $topicId2         |   表示查询所属群信息时用户所加入群的群ID                     |
|   $topicName1       |   表示查询所属群信息时用户所加入群的群名称                    |
|   $topicName2       |   表示查询所属群信息时用户所加入群的群名称	                  |
|   $topicBulletin1   |	  表示查询所属群信息时用户所加入群的群公告                    |
|   $topicBulletin2   |   表示查询所属群信息时用户所加入群的群公告                    |
|   $newBulletin      |	  表示更新群时设置的新群公告                                 |
|   $newTopicName     |   表示更新群时设置的新群名称                                 |
|   $ownerUuid	      |	  表示群主uuid                                             |
|   $ownerAccount     |	  表示群主account(app账号)                                 |
|   $ownerToken	      |	  表示群主token                                            |
|   $userAccount1     |	  表示群成员1号account(app账号)                             |
|   $userAccount2     |	  表示群成员2号account(app账号)                             |
|   $userAccount3     |   表示群成员3号account(app账号)                             |
|   $userAccount4     |	  表示群成员4号account(app账号)                             |
|   $userAccount5     |	  表示群成员5号account(app账号)                             |
|   $userUuid1	      |	  表示userAccount1的uuid（广义上表示任意一个群成员的uuid）    |
|   $userToken1	      |	  表示userAccount1的token（广义上表示任意一个群成员的token）  |


### PS：
```
token的获取使用User.getToken()方法。
uuid的获取使用User.getUuid()方法，uuid由MIMC根据($appId, $appAccount)生成，全局唯一。
身份认证有两种方式：1. token（$ownerToken/$userToken1）; 2. app信息,app帐号（$appKey，$appSecret，$ownerAccount/$userAccount1）。
当两种认证信息都存在时，优先验证前者。前者一般用于app客户端，后者一般用于app服务端。下面给出了这两种的使用方式。
```

### 创建群

#### 如下为$ownerAccount创建群
	
+ HTTPS请求
```
curl "https://mimc.chat.xiaomi.net/api/topic/$appId" -XPOST -d '{"topicName":$topicName,"accounts":"$userAccount1,$userAccount2,$userAccount3"}' -H "Content-Type: application/json" -H "token:$ownerToken"

curl "https://mimc.chat.xiaomi.net/api/topic/$appId" -XPOST -d '{"topicName":$topicName,"accounts":"$userAccount1,$userAccount2,$userAccount3"}' -H "Content-Type: application/json" -H "appKey:$appKey" -H "appSecret:$appSecret" -H "appAccount:$ownerAccount"
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
curl "https://mimc.chat.xiaomi.net/api/topic/$appId/$topicId" -H "Content-Type: application/json" -H "token:$userToken1"

curl "https://mimc.chat.xiaomi.net/api/topic/$appId/$topicId" -H "Content-Type: application/json" -H "appKey:$appKey" -H "appSecret:$appSecret" -H "appAccount:$userAccount1"
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
curl "https://mimc.chat.xiaomi.net/api/topic/$appId/account" -H "Content-Type: application/json" -H "token:$userToken1"

curl "https://mimc.chat.xiaomi.net/api/topic/$appId/account" -H "Content-Type: application/json" -H "appKey:$appKey" -H "appSecret:$appSecret" -H "appAccount:$userAccount1"
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
curl "https://mimc.chat.xiaomi.net/api/topic/$appId/$topicId/accounts" -XPOST -d '{"accounts":"$userAccount4,$userAccount5"}' -H "Content-Type: application/json" -H "token:$userToken1"

curl "https://mimc.chat.xiaomi.net/api/topic/$appId/$topicId/accounts" -XPOST -d '{"accounts":"$userAccount4,$userAccount5"}' -H "Content-Type: application/json" -H "appKey:$appKey" -H "appSecret:$appSecret" -H "appAccount:$userAccount1"
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
curl "https://mimc.chat.xiaomi.net/api/topic/$appId/$topicId/account" -XDELETE -H "Content-Type: application/json" -H "token:$userToken1"

curl "https://mimc.chat.xiaomi.net/api/topic/$appId/$topicId/account" -XDELETE -H "Content-Type: application/json" -H "appKey:$appKey" -H "appSecret:$appSecret" -H "appAccount:$userAccount1"
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
			{"uuid":$userUuid3,"account":$userAccount3},
			{"uuid":$userUuid4,"account":$userAccount4},
			{"uuid":$userUuid5,"account":$userAccount5}
		]
	}
}
```

+ 若是群主退群，则JSON结果如下：
```
{"code":500,"message":"quit topic fail","data":null}
```
 
### 群主踢用户退群

#### 如下为$ownerAccount踢$userAccount4,$userAccount5退出群

+ HTTPS请求
```
curl "https://mimc.chat.xiaomi.net/api/topic/$appId/$topicId/accounts?accounts=$userAccount4,$userAccount5" -XDELETE -H "Content-Type: application/json" -H "token:$ownerToken"

curl "https://mimc.chat.xiaomi.net/api/topic/$appId/$topicId/accounts?accounts=$userAccount4,$userAccount5" -XDELETE -H "Content-Type: application/json" -H "appKey:$appKey" -H "appSecret:$appSecret" -H "appAccount:$ownerAccount"
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

#### 如下为$ownerAccount更新群信息：群主为$userAccount2，群名称为$newTopicName，群公告为$newBulletin
	
+ HTTPS请求
```
curl "https://mimc.chat.xiaomi.net/api/topic/$appId/$topicId" -XPUT -d '{"topicId":$topicId, "ownerUuid":$userUuid2,"topicName":$newTopicName,"bulletin":$newBulletin}' -H "Content-Type: application/json" -H "token:$ownerToken"

curl "https://mimc.chat.xiaomi.net/api/topic/$appId/$topicId" -XPUT -d '{"topicId":$topicId, "ownerUuid":$userUuid2,"topicName":$newTopicName,"bulletin":$newBulletin}' -H "Content-Type: application/json" -H "appKey:$appKey" -H "appSecret:$appSecret" -H "appAccount:$ownerAccount"
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
curl "https://mimc.chat.xiaomi.net/api/topic/$appId/$topicId" -XDELETE -H "Content-Type: application/json" -H "token:$ownerToken"

curl "https://mimc.chat.xiaomi.net/api/topic/$appId/$topicId" -XDELETE -H "Content-Type: application/json" -H "appKey:$appKey" -H "appSecret:$appSecret" -H "appAccount:$ownerAccount"
```

+ JSON结果
```
{"code":200,"message":"success！","data":null}
```

## 消息漫游

### 参数列表

|   Variable   | Meanings  |
| :----------------- | :---------------------------------------------------------|
|  $appId            |  小米开放平台申请的AppId                  		  |
|  $token            |  通过token服务器获得到的token            		          |
|  $account          |  接入方app账号 		             		     |
|  $fromAccount      |  接入方app账号                           		     |
|  $toAccount        |  接入方app账号	                     		     |
|  $topicId          |  表示群ID                                		      |
|  $utcFromTime      |  表示查询开始时间，UTC时间，单位毫秒       		    |
|  $utcToTime        |  表示查询结束时间，UTC时间，单位毫秒       		    |
|  $row              |  表示返回的消息条数                       		  |
|  $messages         |  表示返回的消息集合                       		  |
|  $sequence         |  表示消息sequence	                      		      |
|  $payload	     |  表示经过Base64编码的消息体，app端需要进行Base64解码          |
|  $ts               |  表示消息时间戳                          		    |

### PS：
```
utcFromTime和utcToTime的时间间隔不能超过24小时，查询状态为[utcFromTime,utcToTime)
```

### 拉取单聊消息记录

#### 如下为拉取单聊消息记录
	
+ HTTPS请求(POST)
```
curl https://mimc.chat.xiaomi.net/api/msg/p2p/query/ -XPOST -d '{"appId":$appId,"toAccount":$toAccount,"fromAccount":$fromAccount,"utcFromTime":$utcFromTime,"utcToTime":$utcToTime}' -H "Content-Type: application/json;charset=UTF-8" -H "Accept:application/json;charset=UTF-8" -H "token:$token"
```

+ JSON结果示例
```
{
     "code": 200, 
     "message": "success", 
     "data": {
         "appId": $appId, 
         "toAccount": $toAccount, 
         "fromAccount":$fromAccount, 
         "messages": [
             {
                 "sequence": $sequence, 
                 "payload": $payload, 
                 "ts": $ts
             }
         ], 
         "row": 1
     }
 }
```

### 拉取群聊消息记录

#### 如下为拉取群聊消息记录

+ HTTPS请求(POST)
```
curl https://mimc.chat.xiaomi.net/api/msg/p2t/query/ -XPOST -d '{"appId":$appId,"account":$account,"topicId":$topicId,"utcFromTime":$utcFromTime,"utcToTime":$utcToTime}' -H "Content-Type: application/json;charset=UTF-8" -H "Accept:application/json;charset=UTF-8" -H "token:$token"
```

+ JSON结果示例
```
{
     "code": 200,
     "message": "success",
     "data": {
         "appId": $appId,
         "topicId": $topicId,
         "row": 2,
         "messages": [
             {
                 "sequence": $sequence,
                 "fromAccount": $fromAccount,
                 "payload": $payload,
                 "ts": $ts
             },
             {
                 "sequence": $sequence,
                 "fromAccount": $fromAccount,
                 "payload": $payload,
                 "ts": $ts
             }
         ]
     }
 }
```

## 联系我们

```
欢迎了解MIMC，以下是联系我们的途径。

邮箱📮：mimc-help@xiaomi.com

微信公众号：

QQ群二维码：

微信群二维码：
```

[回到顶部](#readme)
