#### MIMC系统不对传输的消息格式/内容进行理解，在MIMC看来传输的都是二进制数据，消息格式由APP开发者自定义自解析
#### 我们建议开发者参考以下样例来定义自己的消息格式
#### 1. 检查用户在线
+ 用户A`通过MIMC`发送ping给用户B
+ 用户B接收到ping后，`通过MIMC`发送pong给用户A
```
服务器上存储的用户状态会有延迟，所以要获取最精确的用户在线状态，一般用端到端ping-pong方式
```
###### 检查用户在线ping
```
    {
        version: 0, // 建议保留version字段，方便后续协议升级兼容
        msgId: "PING_12345", // APP业务层面自维护消息ID
        type: "PING", // PING|PONG|...
        timestamp: "1516763973000", // 建议精确到毫秒
        payload: "appAccount_A",
    }
```
###### 检查用户在线pong
```
    {
        version: 0, // 建议保留version字段，方便后续协议升级兼容
        msgId: "PONG_12345", // APP业务层面自维护消息ID
        type: "PONG", // PING|PONG|...
        timestamp: "1516763973000", // 建议精确到毫秒
        payload: "appAccount_B",
    }
```

#### 2. 文本消息
+ 用户A`通过MIMC`发送文本消息`(msgId="TEXT_12345")`给用户B
+ 用户B接收文本消息`(msgId="TEXT_12345")`

```
    {
        version: 0, // 建议保留version字段，方便后续协议升级兼容
        msgId: "TEXT_12345", // APP业务层面自维护消息ID
        type: "TEXT", // TEXT|PIC_FILE|AUDIO_FILE|BIN_FILE|...
        timestamp: "1516763973000", // 建议精确到毫秒
        payload: "欢迎使用小米即时消息云(MIMC)",
    }
```

#### 3.多媒体消息

+ 用户A将图片文件/语音文件/视频文件`(非实时语音视频聊天)`上传到文件存储服务器，获得一个URL
+ 用户A`通过MIMC`发送多媒体消息`(msgId="PIC_FILE_12345", payload=URL)`给用户B
+ 用户B接收多媒体消息`(msgId="PIC_FILE_12345", payload=URL)`，通过URL下载图片文件/语音文件/视频文件
```
    {
        version: 0, // 建议保留version字段，方便后续协议升级兼容
        msgId: "PIC_FILE_12345", // APP业务层面自维护消息ID
        type: "PIC_FILE", // TEXT|PIC_FILE|AUDIO_FILE|BIN_FILE|...
        timestamp: "1516763973000", // 建议精确到毫秒
        payload: "https://github.com/Xiaomi-mimc/operation-manual/blob/master/img-folder/MIMC-Official-Accounts.jpg",
    }
```

#### 4. 撤回消息
+ 用户A发送文本消息`(msgId="TEXT_12345")`给用户B
+ 用户B接收文本消息`(msgId="TEXT_12345")`
+ 用户A发送撤回消息`(msgId="TEXT_RECALL_12345")`给用户B
+ 用户B接收撤回消息`(msgId="TEXT_RECALL_12345")`并删除文本消息`(msgId="TEXT_12345")`
```
    {
        version: 0, // 建议保留version字段，方便后续协议升级兼容
        msgId: "TEXT_RECALL_12345", // APP业务层面自维护消息ID
        type: "TEXT_RECALL", // TEXT_RECALL|PIC_FILE_RECALL|AUDIO_FILE_RECALL|BIN_FILE_RECALL|...
        timestamp: "1516763973090", // 建议精确到毫秒
        payload: {recall_msgId: "TEXT_12345"}, // 撤回msgId为TEXT_12345的消息
    }
```

#### 5. 已读消息
+ 用户A发送文本消息`(msgId="TEXT_12345")`给用户B
+ 用户B接收文本消息`(msgId="TEXT_12345")`
+ 用户B发送已读消息`(msgId="TEXT_READ_12345")`给用户A
+ 用户A接收已读消息`(msgId="TEXT_READ_12345")`并标记文本消息`(msgId="TEXT_12345")`已读

```
    {
        version: 0, // 建议保留version字段，方便后续协议升级兼容
        msgId: "TEXT_READ_12345", // APP业务层面自维护消息ID
        type: "TEXT_READ", // TEXT_READ|PIC_FILE_READ|AUDIO_FILE_READ|BIN_FILE_READ|...
        timestamp: "1516763973134", // 建议精确到毫秒
        payload: {read_msgId: "TEXT_12345"}, // 已读msgId为TEXT_12345的消息
    }
```

#### 6. 添加好友
+ 用户A发送添加好友请求`(msgId="ADD_FRIEND_REQUEST_12345")`给用户B
+ 用户B接收添加好友请求`(msgId="ADD_FRIEND_REQUEST_12345")`
+ 用户B同意/拒绝好友添加请求，则回复同意/拒绝`(msgId="ADD_FRIEND_RESPONSE_12345")`给用户A，并更新服务器好友数据

```
添加好友请求消息建议格式：
{
    version: 0, // 建议保留version字段，方便后续协议升级兼容
    msgId: "ADD_FRIEND_REQUEST_12345", // APP业务层面自维护消息ID
    type: "ADD_FRIEND_REQUEST", // TEXT_READ|PIC_FILE_READ|AUDIO_FILE_READ|BIN_FILE_READ|...
    timestamp: "1516763973134", // 建议精确到毫秒
    payload: {add_requester: "accountA"},
}

同意/拒绝消息建议格式：
{
    version: 0, // 建议保留version字段，方便后续协议升级兼容
    msgId: "ADD_FRIEND_RESPONSE_12345", // APP业务层面自维护消息ID
    type: "ADD_FRIEND_RESPONSE", // TEXT_READ|PIC_FILE_READ|AUDIO_FILE_READ|BIN_FILE_READ|...
    timestamp: "1516763973134", // 建议精确到毫秒
    payload: {add_responser: "accountB", accepted:true/false},
}
```

#### 7. 其他自定义消息功能
+ 参考以上，设置对应的type/payload