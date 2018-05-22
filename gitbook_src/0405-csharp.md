## 目录
* [C#应用配置修改](#C#应用配置修改)
* [用户初始化](#用户初始化)
* [安全认证](#安全认证)
* [登录](#登录)
* [在线状态变化回调](#在线状态变化回调)
* [发送单聊消息](#发送单聊消息)
* [接收消息回调](#接收消息回调)
* [注销](#注销)
* [更新日志](https://github.com/Xiaomi-mimc/mimc-csharp-sdk/blob/master/sdk/UPDATE.md)

## C#应用配置修改
```
1、可先下载demo，并更新demo项目依赖路径到 sdk/0.0.X，建议采用最新版本，然后运行demo；
2、在项目中添加sdk目录中最新的dll包：
   |-- mimc-csharp-sdk.dll
      |--Newtonsoft.Json.dll
      |--protobuf-net.dll
      |--StreamJsonRpc.dll
      |--log4net.dll
3、项目采用Visual Studio 2017开发；
4、日志采用log4net组件，项目中已经做了简单配置，可以按照需要自行修改；
```

## 用户初始化

```
/**
 * @param[appId]: 开发者在小米开放平台申请的appId
 * @param[appAccount]: 用户在APP帐号系统内的唯一帐号ID
 **/
User user = new User(appId, appAccount);
```

## 安全认证
#### 参考 [详细文档安全认证](https://github.com/Xiaomi-mimc/operation-manual/blob/master/README.md#%E5%AE%89%E5%85%A8%E8%AE%A4%E8%AF%81)
```
user.RegisterTokenFetcher(IMIMCTokenFetcher fetcher);
```
实现接口：
```
interface IMIMCTokenFetcher {
    /// <summary>
    /// MIMCUser如果采用Login()方式登录 则必须实现该接口
    ///  FetchToken()访问APP应用方自行实现的AppProxyService服务，
    ///  该服务实现以下功能：
    ///1. 存储appId/appKey/appSecret(appKey/appSecret不可存储在APP客户端，以防泄漏)
    ///2. 用户在APP系统内的合法鉴权
    ///3. 调用小米TokenService服务，并将小米TokenService服务返回结果通过fetchToken()原样返回
    /// </summary>
    /// <returns>小米TokenService服务下发的原始数据</returns>
    string FetchToken();

    /// <summary>
    ///  MIMCUser如果采用LoginAsync()方式登录 则必须实现该接口
    /// </summary>
    /// <returns>小米TokenService服务下发的原始数据</returns>
    Task<string> FetchTokenAsync();
}
```

## 登录

```
/**
 * @note: 用户登录接口，除在APP初始化时调用，APP从后台切换到前台时也建议调用一次
 */
user.Login();
user.LoginAsync();
```

## 在线状态变化回调

```
  ///添加事件
  user.StateChangeEvent += HandleStatusChange;
  public void HandleStatusChange(object source, StateChangeEventArgs e)
  {
       logger.InfoFormat("{0} OnlineStatusHandler status:{1},errType:{2},errReason:{3},errDescription:{4}!",e.User.AppAccount(), e.IsOnline, e.ErrType, e.ErrReason, e.ErrDescription);
  }
```

## 发送单聊消息

```
  /**<summary>
  * 发送单聊消息
  * </summary>
  *  <param name="toAppAccount">消息接收者在APP帐号系统内的唯一帐号ID</param>
  *  <param name="msg">开发者自定义消息体，二级制数组格式</param>
  * <returns>packetId客户端生成的消息ID</returns>
  **/
String packetId = user.SendMessage(string toAppAccount, byte[] msg)
String packetId = user.SendMessageAsync(string toAppAccount, byte[] msg)

  /**    <summary>
  * 发送单聊消息
  * </summary>
  *  <param name="toAppAccount">消息接收者在APP帐号系统内的唯一帐号ID</param>
  *  <param name="msg">开发者自定义消息体，二级制数组格式</param>
  *  <param name="isStore">是否保存历史记录，true：保存，false：不存</param>
  * <returns>packetId客户端生成的消息ID</returns>
  **/
String packetId = user.SendMessage(string toAppAccount, byte[] msg, bool isStore)
String packetId = user.SendMessageAsync(string toAppAccount, byte[] msg, bool isStore)

```
## 发送群聊消息

```
   /// <summary>
   /// 发送群聊消息
   /// </summary>
   /// <param name="topicId">群ID</param>
   /// <param name="msg">开发者自定义消息体，二级制数组格式</param>
   /// <returns>packetId客户端生成的消息ID</returns>
String packetId = user.SendGroupMessage(long topicId, byte[] msg)
String packetId = user.SendGroupMessageAsync(long topicId, byte[] msg)


   /// <summary>
   /// 发送群聊消息
   /// </summary>
   /// <param name="topicId">群ID</param>
   /// <param name="msg">开发者自定义消息体，二级制数组格式</param>
   /// <param name="isStore">是否保存历史记录，true：保存，false：不存</param>
   /// <returns>packetId客户端生成的消息ID</returns>
String packetId = user.SendGroupMessage(long topicId, byte[] msg, bool isStore)
String packetId = user.SendGroupMessageAsync(long topicId, byte[] msg, bool isStore)

```

## 接收消息回调

```
///添加事件
user.MessageEvent += HandleMessage;
user.MessageTimeOutEvent += HandleMessageTimeout;
user.GroupMessageEvent += HandleGroupMessage;
user.GroupMessageTimeoutEvent += HandleGroupMessageTimeout;
user.ServerACKEvent += HandleServerACK;
public void HandleMessageTimeout(object source, SendMessageTimeoutEventArgs e)
{
    P2PMessage msg = e.P2PMessage;
    logger.InfoFormat("MIMCMessageHandler HandleSendMessageTimeout, to:{0}, packetId:{1}, sequence:{2}, ts:{3}, payload:{4}",
    e.User.AppAccount(), msg.PacketId, msg.Sequence, msg.Timestamp,
    Encoding.UTF8.GetString(msg.Payload));
}

public void HandleGroupMessage(object source, GroupMessageEventArgs e)
{
     List<P2TMessage> packets = e.Packets;
     logger.InfoFormat("MIMCMessageHandler HandleGroupdMessage, to:{0}, packetCount:{1}", e.User.AppAccount(), packets.Count);
     if (packets.Count == 0)
     {
         logger.WarnFormat("HandleGroupdMessage packets.Count==0");
         return;
     }
     foreach (P2TMessage msg in packets)
     {
         logger.InfoFormat("MIMCMessageHandler HandleGroupdMessage, to:{0}, packetId:{1}, sequence:{2}, ts:{3}, payload:{4}",
         e.User.AppAccount(), msg.PacketId, msg.Sequence, msg.Timestamp,
         Encoding.UTF8.GetString(msg.Payload));
     }
}

public void HandleGroupMessageTimeout(object source, SendGroupMessageTimeoutEventArgs e)
{
    P2TMessage msg = e.P2tMessage;
    logger.InfoFormat("MIMCMessageHandler HandleSendGroupdMessageTimeout, to:{0}, packetId:{1}, sequence:{2}, ts:{3}, payload:{4}",
    e.User.AppAccount(), msg.PacketId, msg.Sequence, msg.Timestamp,
    Encoding.UTF8.GetString(msg.Payload));
}

public void HandleServerACK(object source, ServerACKEventArgs e)
{
    ServerAck serverAck = e.ServerAck;
    logger.InfoFormat("{0} MIMCMessageHandler HandleServerACK, appAccount:{0}, packetId:{1}, sequence:{2}, ts:{3}",
    e.User.AppAccount(), serverAck.PacketId, serverAck.Sequence, serverAck.Timestamp);
}
```

## 注销

```
user.Logout();
```

[回到顶部](#readme)




