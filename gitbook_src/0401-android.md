## 目录
* [安卓应用配置修改](#安卓应用配置修改)
* [用户初始化](#用户初始化)
* [安全认证](#安全认证)
* [登录](#登录)
* [在线状态变化回调](#在线状态变化回调)
* [发送单聊消息](#发送单聊消息)
* [发送群聊消息](#发送群聊消息)
* [接收消息回调](#接收消息回调)
* [注销](#注销)

## 安卓应用配置修改
#### 包名"com.xiaomi.mimcdemo"必须替换成APP自己的包名
``` xml
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
<uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
<uses-permission android:name="com.xiaomi.xmsf.permission.LOG_PROVIDER" />

<!-- 这里的包名"com.xiaomi.mimcdemo"必须替换成App自己的包名 -->
<permission
    android:name="com.xiaomi.mimcdemo.permission.MIMC_RECEIVE"
    android:protectionLevel="signature" />
<uses-permission android:name="com.xiaomi.mimcdemo.permission.MIMC_RECEIVE" />

<service
    android:name="com.xiaomi.mimc.MIMCService"
    android:enabled="true"
    android:exported="false" />

<service
    android:name="com.xiaomi.mimc.MIMCCoreService"
    android:enabled="true"
    android:exported="false"
    android:process=":mimc"/>

<service
    android:name="com.xiaomi.mimc.MIMCJobService"
    android:enabled="true"
    android:exported="false"
    android:permission="android.permission.BIND_JOB_SERVICE"
    android:process=":mimc" />

<receiver
    android:name="com.xiaomi.mimc.receivers.MIMCReceiver"
    android:exported="true">
    <intent-filter>
    <action android:name="com.xiaomi.channel.PUSH_STARTED" />
    <action android:name="com.xiaomi.push.service_started" />
    <action android:name="com.xiaomi.push.channel_opened" />
    <action android:name="com.xiaomi.push.channel_closed" />
    <action android:name="com.xiaomi.push.new_msg" />
    <action android:name="com.xiaomi.push.kicked" />
    </intent-filter>
</receiver>

<receiver android:name="com.xiaomi.mimc.receivers.MIMCPingReceiver">
    <intent-filter>
    <action android:name="com.xiaomi.push.PING_TIMER" />
    </intent-filter>
</receiver>
```
#### 备注：
```
我们将MIMCCoreService / MIMCJobService定义在了mimc进程中。
开发者也可以配置其运行在任意进程，如果没有配置android:process这个属性，那么它们将运行在应用的主进程中。
```

## 用户初始化

``` java
MIMCClient.initialize(this);

/**
 * @param[appAccount]: 用户在APP帐号系统内的唯一帐号ID
 */
MIMCUser user = new MIMCUser(appAccount);
```

## 安全认证
#### 参考 [安全认证](03-auth.html)
``` java
user.registerTokenFetcher(MIMCTokenFetcher fetcher);
interface MIMCTokenFetcher {
    /**
     * @note: fetchToken()访问APP应用方自行实现的AppProxyService服务，该服务实现以下功能：
            1. 存储appId/appKey/appSecret(appKey/appSecret不可存储在APP客户端，以防泄漏)
            2. 用户在APP系统内的合法鉴权
            3. 调用小米TokenService服务，并将小米TokenService服务返回结果通过fetchToken()原样返回
    * @return: 小米TokenService服务下发的原始数据
    */
    public String fetchToken();
}
```

## 登录

``` java
/**
 * @note: 用户登录接口，除在APP初始化时调用，APP从后台切换到前台时也建议调用一次
 */
user.login();
```

## 在线状态变化回调

``` java
user.registerOnlineStatusListener(MIMCOnlineStatusListener listener);
interface MIMCOnlineStatusListener {
    /**
　　　* @param[status]: 登录状态，MIMCConstant.STATUS_LOGIN_SUCCESS 在线，MIMCConstant.STATUS_LOGOUT 离线。
　　　* @param[code]: 状态码
　　　* @param[msg]: 状态描述
     */
    public void onStatusChanged(int status, int code, String msg);
}
```

## 发送单聊消息

``` java
/**
 * @param[toAppAccount]: 消息接收者在APP帐号系统内的唯一帐号ID
 * @param[payload]: 开发者自定义消息体
 * @param[isStore]: 消息是否存储在mimc服务端，true存储，false不存储，默认存储
 * @return: 客户端生成的消息ID
 */
String packetId = user.sendMessage(String toAppAccount, byte[] payload, boolean isStore);
```

## 发送群聊消息

``` java
/**
 * @param[groupId]: 群ID，也称为topicId
 * @param[payload]: 开发者自定义消息体
 * @param[isStore]: 消息是否存储在mimc服务端，true存储，false不存储，默认存储
 * @return: 客户端生成的消息ID
 */
String packetId = user.sendGroupMessage(long groupID, byte[] payload, boolean isStore);
```

## 接收消息回调

``` java
user.registerMessageHandler(MIMCMessageHandler handler);
interface MIMCMessageHandler {
    /**
     * @param[packets]: 单聊消息集
     * @note: MIMCMessage 单聊消息
     *        MIMCMessage.packetId: 消息ID
     *        MIMCMessage.sequence: 序列号
     *        MIMCMessage.fromAccount: 发送方帐号
     *        MIMCMessage.toAccount: 接收方帐号
     *        MIMCMessage.payload: 消息体
     *        MIMCMessage.timestamp: 时间戳
     */
    public void handleMessage(List<MIMCMessage> packets);

    /**
     * @param[packets]: 群聊消息集
     * @note: MIMCGroupMessage 群聊消息
     *        MIMCGroupMessage.packetId: 消息ID
     *        MIMCGroupMessage.groupId: 群ID
     *        MIMCGroupMessage.sequence: 序列号
     *        MIMCGroupMessage.fromAccount: 发送方帐号
     *        MIMCGroupMessage.payload: 消息体
     *        MIMCGroupMessage.timestamp: 时间戳
     */
    public void handleGroupMessage(List<MIMCGroupMessage> packets);

    /**
     * @param[serverAck]: 服务器返回的serverAck对象
     *       serverAck.packetId: 客户端生成的消息ID
     *       serverAck.timestamp: 消息发送到服务器的时间(单位:ms)
     *       serverAck.sequence: 服务器为消息分配的递增ID，单用户空间内递增唯一，可用于去重/排序
    */
    public void handleServerAck(MIMCServerAck serverAck);

    /**
     * @param[message]: 发送超时的单聊消息
     */
    public void handleSendMessageTimeout(MIMCMessage message);

    /**
     * @param[groupMessage]: 发送超时的群聊消息
     */
    public void handleSendGroupMessageTimeout(MIMCGroupMessage groupMessage);
}
```

## 注销

``` java
user.logout();
```

[回到顶部](#readme)
