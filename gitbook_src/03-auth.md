## 安全认证

#### APP客户端获取Token的逻辑如下：
```
    APP <--> AppProxyService(APP开发者实现) <--> 小米TokenService(MIMC)
```
#### APP <--> AppProxyService(APP开发者实现)
######  安卓APP：
```
实现MIMCTokenFetcher：
   访问AppProxyService，从AppProxyService返回结果中获取[小米TokenService下发的原始数据]
```
###### iOS APP：
```
初始化NSMutableURLRequest：
    SDK调用NSMutableURLRequest，异步访问AppProxyService
实现delegate parseTokenDelegate：
    解析NSMutableURLRequest返回结果，获取[小米TokenService下发的原始数据]
```
###### Web：
```
实现function fetchMIMCToken()：
   访问AppProxyService，从AppProxyService返回结果中获取[小米TokenService下发的原始数据]
```
#### AppProxyService(APP开发者实现)需实现以下功能：
```
    1. 存储appId/appKey/appSecret(appKey/appSecret不应存储在客户端，防止泄露)
    2. 用户在APP系统内的合法鉴权
    3. 调用小米TokenService服务，并将[小米TokenService下发的原始数据]返回客户端