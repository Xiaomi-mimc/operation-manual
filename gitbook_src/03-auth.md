## 安全认证


#### APP客户端获取Token的逻辑如下：
```
    APP(fetchToke开发者实现) <--> AppProxyService(APP开发者实现) <--> 小米TokenService(MIMC)
```
#### 开发者实现fetchToken访问 AppProxyService
```
  访问AppProxyService，从AppProxyService返回结果中获取[小米TokenService下发的原始数据]并返回
```

#### AppProxyService需实现以下功能：
```
    1. 存储appId/appKey/appSecret(appKey/appSecret不应存储在客户端，防止泄露)
    2. 用户在APP系统内的合法鉴权
    3. 调用小米TokenService服务，并将[小米TokenService下发的原始数据]
```

#### AppProxyService调用小米TokenService的方式如下：
###### 参数列表

|   Variable          | Meanings  |
| :------------------ | :--------------------------------------------------------------|
|   $appId            |   小米开放平台申请的AppId                                       |
|   $appKey           |   小米开放平台申请的AppKey                                      |
|   $appSecret        |   小米开放平台申请的AppSecret                                   |
|   $appPackage       |   小米开放平台申请的AppPackage                                  |
|   $appAccount       |   用户在APP帐号系统内唯一ID                                     |
|   $chid             |   MIMC服务的标识，为常量9                           |
|   $uuid             |   $appAccount在MIMC内对应userId，开发者可忽略                   |
|   $token          |   $appAccount在MIMC系统中的token                               |

+ HTTP 请求
```
    curl "https://mimc.chat.xiaomi.net/api/account/token" -XPOST
      -d '{"appId":$appId,"appKey":$appKey,"appSecret":$appSecret,"appAccount":$appAccount}'
      -H "Content-Type: application/json"
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
#### 备注：对于以上JSON结果，APP不需要理解其格式，通过MIMCTokenFetcher(安卓)/parseTokenDelegate(iOS)原样返回即可，其他平台类似

