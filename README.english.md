# operation-manual

## Contents

* [Frequently Asked Questions](#frequently-asked-questions)
    * [Are there any charges?](#are-there-any-charges)
    * [What scenarios does this apply to?](#what-scenarios-does-this-apply-to)
    * [Which platforms are supported?](#which-platforms-are-supported)
    * [What development work is required?](#what-development-work-is-required)
        * [developers need to implement their own chat interface](#developers-need-to-implement-their-own-chat-interface)
        * [developers need to access the message cloud security authentication](#developers-need-to-access-the-message-cloud-security-authentication)
        * [developers need to define their own message formats](#developers-need-to-define-their-own-message-formats)
    * [Why don't we provide a chat interface?](#why-dont-we-provide-a-chat-interface)
    * [why developers need to define their own message formats](#why-do-developers-need-to-define-their-own-message-formats)
    * [do developers need to maintain account mapping?](#do-developers-need-to-maintain-account-mapping)
    * [How to do if app does not receive messages in the background](#how-to-do-if-app-does-not-receive-messages-in-the-background)
* [Overall Architecture](#overall-architecture)
* [Send and Receive Messages](#send-and-receive-messages)
    * [Android](https://github.com/Xiaomi-mimc/mimc-android-sdk)
    * [Web](https://github.com/Xiaomi-mimc/mimc-webjs-sdk)
    * [iOS](https://github.com/Xiaomi-mimc/mimc-ios-sdk)
* [How to Access](#how-to-access)


* [Security Authentication](#security-authentication)
* [Recommended Message Format](#recommended-message-format)
    * [Check online users](#check-online-users)
    * [Text messages](#text-messages)
    * [Multimedia messages](#multimedia-messages)
    * [Recall messages](#recall-messages)
    * [Read messages](#read-messages)
* [Chat Across Applications](#chat-across-applications)
* [Push Messages](#push-messages)
    * [Push one-on-one chat information](#push-one-one-one-chat-information)
    * [Push group chat information](#push-group-chat-information)
* [Message Callback](#message-callback)
    * [P2P instant message callback](#p2p-instant-message-callback)
    * [P2P offline message callback](#p2p-offline-message-callback)
    * [Topic instant message callback](#topic-instant-message-callback)
    * [topic offline message callback](#topic-offline-message-callback)
* [Group Chat Information](#group-chat-information)
    * [Create a topic](#create-a-topic)
    * [Query information on specified topic](#query-information-on-specified-topic)
    * [Query all topic attributes](#query-all-topic-attributes)
    * [invite users to join topic](#invite-user-to-join-a-topic)
    * [Topic member leave topic](#topic-member-leave-topic)
    * [Topic owner kicks member](#topic-owner-kicks-member)
    * [Topic owner updates topic information](#topic-owner-updates-topic-information)
    * [Topic owner destroys topic](#topic-owner-destroys-topic)
* [Message Roaming](#message-roaming)
    * [Pull one-on-one chat record](#pull-one-on-one-chat-record)
    * [Pull group chat record](#pull-group-chat-record)
* [Contact Us](#contact-us)

## Frequently Asked Questions

#### Are there any charges?

    Instant message services will always be free for everyone to use, so developers should not worry
    We will not restrict any of the instant messaging cloud functions, which will be provided for everyone to use for free, including but not limited to:
    number of sent messages, number of registered users, multiple terminal login, number of topics created, group message history, real-time/off-line message callback, and number of push messages.
    Of course, we still retain the right to ban users who abuse the system


#### What scenarios does this apply to?

    MIMC applies to all messaging scenarios, and is not limited to one-on-one chat/group chat/online customer service/personal messages/IoT signaling etc.


#### Which platforms are supported?

    Currently MIMC supports Android/IOS/web platforms. If developers have other platform development requirements, please contact us in advance


* [Contact Us](#contact-us)

#### What development work is required?

###### Developers need to implement their own chat interface

###### Developers need to access the message cloud security authentication

    See:


* [How to Access](#how-to-access)
* [Security Authentication](#security-authentication)

###### Developers need to define their own message formats

    See:


* [Recommended Message Format](#recommended-message-format)
    * [Check online users](#check-online-users)
    * [Text messages](#text-messages)
    * [Multimedia messages](#multimedia-messages)
    * [Recall messages](#recall-messages)
    * [Read messages](#read-messages)

#### Why don't we provide a chat interface?

    We do not provide a unified chat UI for the following reasons:  
    1. Each app has its own style. Due to this diversity, it is impossible for a single UI to meet everyone's needs  
    2. For developers, the costs of developing a UI are not high  
    3. When developers develop their own UI, they are able to 100% customize the interface and functions
    So, we think it is appropriate for developers to customize their own UI based on the style of their app

#### Why do developers need to define their own message formats?

    We do not provide a unified message format, and allow the developers to customize their own message format for the following reasons:
    1. Apps require different functions. Some require a "read" function, while others do not.
       So we provide a recommended message format,
       and developers can define the most appropriate message format according to their own situation
    2. MIMC (Xiaomi Instant Message Cloud) has a broad range of application scenarios.
       IM chat is just one of the MIMC application scenarios,
       there are also many different messaging scenarios such as IoT signalling.
    So, we think it is appropriate for developers to customize their own message format
       based on the style of their app while referring to our recommended message format

#### Do developers need to maintain account mapping?

    No.
    MIMC user login/messaging etc. use the account ID in the app's own account system.
    The MIMC account system is transparent to app developers


###### Why did we do it like this?

    When app developers connect to other IM providers, they need to access the IM provider service and register a new ID for each appAccount,
    and developers need to store the following information in their own backend system:
      1. appAccount --> The IM provider system ID
      2. The IM provider system ID + the IM provider login password (plain text)
    This has the following drawbacks:
      1. The account mapping maintenance costs for the developer are high, and when errors appear, they are hard to correct
      2. Storing login password in plain text, security is very poor and developers take a very high security risk
    Therefore, MIMC (Xiaomi instant message cloud) does not adopt the above method.
    MIMC maintains its own account mapping to ensure that the MIMC ID is transparent to developers.
    This not only reduces the burden on developers and increases account security,
    it also allows developers to feel than the MIMC is their "own" message system.


###### For example:

       Assume the developer has an APP called "Yantu Wenxue" which is Integrated into the MIMC (Xiaomi instant message cloud) service
       Assume that the appId of "Yantu Wenxue" on the Xiaomi open platform is "580012345678"
         User A logs on to "Yantu Wenxu" using the account name mobile phone number "13800000001"
         User B logs on to "Yantu Wenxu" using the account name mobile phone number "13800000002"

       The first time User A logs on to "Yantu Wenxu"
         userA = new User("580012345678", "13800000001");
         userA.login();
       The MIMC backend service will generate MIMC ID("8049600000000001") for User A, and maintains the following map:：
         "580012345678" + "13800000001" --> "8049600000000001"

       Then User A sends a message to User B
         userA.sendMessage("13800000002", "Hello B");
       Because User B has not yet logged on to "Yantu Wenxue",
       MIMC will automatically create a MIMC ID("8049600000000002") for User B
       and maintains the following map:
         "580012345678" + "13800000002" --> "8049600000000002"
       and the message "Hello B" is temporarily stored on the server

       When User B logs on to "Yantu Wenxue"
         userB = new User("580012345678", "13800000002");
         userB.login();
       As User B already has an ID on MIMC,  the MIMC backend service will not allocate a new ID for User B
       When the MIMC backend service detects User B's log in, it will issue the offline message "Hello B" to User B


#### How to do if app does not receive messages in the background

    On the IOS platform, when the app enters the background, the process code execution is paused,
    and tcp connection will closed after a period of time. Currently, Android is slowly converging with iOS.
    In the context of increasingly strict restrictions on app background operations,
    how can we allow the app to "receive" messages when it is running in the background.
    We provide the following recommendations:
        1. Developers develop an offline message service OfflineMessageService, receiving offline messages for MIMC service callbacks(see <offline message callback>).
        2. The OfflineMessageService will receive offline messages, using system-level push services such as Xiaomi push/APNS,
           then send the offline message alert to the user's mobile phone notification bar
        3. When the user clicks on the alert in the notification bar, the app is launched in the foreground,
           and the offline message is automatically received without any more work.

    Note:
        It is recommended that developers automatically trigger the login operation after the app enters the foreground
        (if the user is currently logged in, then their is no effect; if the user is not logged in, this will trigger the log in operation)


## Overall Architecture

<div align="center"><img width="900" height="600" src="https://github.com/Xiaomi-mimc/operation-manual/blob/master/img-folder/MIMC-Arch.jpg"/></div>

## Send and Receive Messages

| Application Platform |                               Description                               |                           Notes                           |
|:--------------------:|:-----------------------------------------------------------------------:|:---------------------------------------------------------:|
|         Web          | The same user is allowed to log in to multiple UA at the same time      | Multi-platform and multi-terminal message synchronization |
|         iOS          | The same user is allowed to log in to multiple devices at the same time | Multi-platform and multi-terminal message synchronization |
|       Android        | The same user is allowed to log in to multiple devices at the same time | Multi-platform and multi-terminal message synchronization |

#### 1) [Android](https://github.com/Xiaomi-mimc/mimc-android-sdk)

#### 2) [Web](https://github.com/Xiaomi-mimc/mimc-webjs-sdk)

#### 3) [iOS](https://github.com/Xiaomi-mimc/mimc-ios-sdk)

## How to Access

App developers can visit the Xiaomi open platform (dev.mi.com) to apply for appId/appKey/appSecret.

The steps are as follows: Log on to the Xiaomi open platform web page -> "management console" -> "Xiaomi app store" -> "create app" -> fill in the app name and package name -> "create" -> Note down the appid/appkey/appsecret.

#### Note: It is recommended that the the app information for MIMC and Xiaomi push is consistent

#### Note: It is sufficient for Android/iOS/Web to use one app, no need to request multiple apps

## Security Authentication

#### The app client logic to obtain the token is as follows:

      APP <--> AppProxyService(implemented by app developer) <--> Xiaomi TokenService(MIMC)


#### APP <--> AppProxyService(implemented by app developer)

######  Android app:

    Implement MIMCTokenFetcher:
      Access AppProxyService, get [raw result from Xiaomi TokenService] from the result returned by the AppProxyService


###### iOS app:

    Initialize NSMutableURLRequest：
      SDK call NSMutableURLRequest, asynchronous access AppProxyService
    Implement delegate parseTokenDelegate:
      Resolve NSMutableURLRequest results to get [raw result from Xiaomi TokenService]


###### Web:

    Initialize: function fetchMIMCToken():
      Access AppProxyService, get [raw result from Xiaomi TokenService] from the result returned by the AppProxyService


#### AppProxyService (implemented by app developer) needs to perform the following functions:

       1. Store appid/appKey/appSecret (appKey/appSecret should not be stored on the client side to prevent leaks)
       2. Verification of users within the app system
       3. Call Xiaomi TokenService, and return the [raw result from Xiaomi TokenService] to the client


#### The AppProxyService access to the Xiaomi TokenService is as follows:

###### Parameter list

| Variable            | Meanings                                                                      |
|:------------------- |:----------------------------------------------------------------------------- |
| $appId              | AppID on the Xiaomi open platform                                             |
| $appKey             | AppKey on the Xiaomi open platform                                            |
| $appSecret          | AppSecret on the Xiaomi open platform                                         |
| $appPackage         | AppPackage on the Xiaomi open platform                                        |
| $appAccount         | Unique user ID on the app account system                                      |
| $chid               | MIMC service identification, fixed at 9                                       |
| $uuid               | Corresponding userID of $appAccount within MIMC, can be skipped by developers |
| $token              | Token of $appAccount within MIMC system                                       |

* HTTP Request
```
    curl "https://mimc.chat.xiaomi.net/api/account/token" -XPOST
      -d '{"appId":$appId,"appKey":$appKey,"appSecret":$appSecret,"appAccount":$appAccount}'
      -H "Content-Type: application/json"
```

* JSON Results
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

#### Note: The app does not need to understand the format of the above JSON results. They can be returned via MIMCTokenFetcher (Android) / parseTokenDelegate (iOS) in their original format

[Back to Top](#readme)

## Recommended Message Format

#### The MIMC system does not interpret the message format/content of the transmission. As far as MIMC is concerned, it is transmitting binary data. The message format is customized by the app developer

#### We recommend that developers refer to the following example to define their own message formats

#### Check online users

* User A sends a ping to User B `through MIMC`
* When User B receives a ping, User B sends a pong to User A `through MIMC`

```
There is a delay in the user status stored on the server,
so to get the most accurate online status for users, the end-to-end ping-pong method is generally used
```

###### Check online users ping
```
    Recommended Ping format is as follows:
    {
        version: 0, // Recommend the version field is retained for subsequent upgrade compatiblity
        msgId: "PING_12345", // APP business level message ID
        msgType: "PING", // PING|PONG|...
        timestamp: "1516763973000", // Recommend this is accurate to the millisecond
        payload: "appAccount_A",
    }
```

###### Check online users pong
```
    Recommended Pong format is as follows:
    {
        version: 0, // Recommend the version field is retained for subsequent upgrade compatiblity
        msgId: "PONG_12345", // APP business level message ID
        msgType: "PONG", // PING|PONG|...
        timestamp: "1516763973000", // Recommend this is accurate to the millisecond
        payload: "appAccount_B",
    }
```

#### Text messages

* User A sends a text message `(msgId="TEXT_12345")` to User B `through MIMC`
* User B receives the text message `(msgId="TEXT_12345")`
```
    Recommended text message format is as follows
    {
       version: 0, // Recommend the version field is retained for subsequent upgrade compatiblity
       msgId: "TEXT_12345", // APP business level message ID
       msgType: "TEXT", // TEXT|PIC_FILE|AUDIO_FILE|BIN_FILE|...
       timestamp: "1516763973000", // Recommend this is accurate to the millisecond
       payload: "Welcome to the Xiaomi instant message cloud (MIMC)",
    }
```

#### Multimedia messages

* User A uploads an image file/voice file/video file `(recorded voice/video chat)` to the file storage server and gets a URL
* User A sends a multimedia message `msgId="PIC_FILE_12345", payload=URL` to User B `through MIMC`
* User B receives the multimedia message`(msgId="PIC_FILE_12345", payload=URL)`, and downloads the image file/voice file/video file via the URL
```
    Recommended multimedia message format is as follows
    {
        version: 0, // Recommend the version field is retained for subsequent upgrade compatiblity
        msgId: "PIC_FILE_12345", // APP business level message ID
        msgType: "PIC_FILE", // TEXT|PIC_FILE|AUDIO_FILE|BIN_FILE|...
        timestamp: "1516763973000", // recommend this is accurate to the millisecond
        payload: "https://github.com/Xiaomi-mimc/operation-manual/blob/master/img-folder/MIMC-Official-Accounts.jpg",
    }
```

#### Recall messages

* User A sends a text message `(msgId="TEXT_12345")` to User B
* User B receives the text message `(msgId="TEXT_12345")`
* User A sends a text recall `(msgId="TEXT_RECALL_12345")` to User B
* User B receives the recall message `(msgid="text_recall_12345")` and deletes the text message `(msgid="text_12345")`
```
    Recommended recall message format is as follows
    {
       version: 0, // Recommend the version field is retained for subsequent upgrade compatiblity
       msgId:  "TEXT_RECALL_12345", // APP business level message ID
       msgType: "TEXT_RECALL", // TEXT_RECALL|PIC_FILE_RECALL|AUDIO_FILE_RECALL|BIN_FILE_RECALL|...
       timestamp: "1516763973090", // Recommend this is accurate to the millisecond
       payload: {recall_msgId: "TEXT_12345"}, // Recalled msgId is TEXT_12345
    }
```

#### Read messages

* User A sends a text message `(msgId="TEXT_12345")` to User B
* User B receives the text message `(msgId="TEXT_12345")`
* User B has sent a text read message `(msgId="TEXT_READ_12345")` to User A
* User B receives the text read message `(msgId="TEXT_READ_12345")` and marks the text message`(msgId="text_12345")` as read
```
    Recommended read message format is as follows
    {
        version: 0, // Recommend the version field is retained for subsequent upgrade compatiblity
        msgId:  "TEXT_READ_12345", // APP business level message ID
        msgType: "TEXT_READ", // TEXT_READ|PIC_FILE_READ|AUDIO_FILE_READ|BIN_FILE_READ|...
        timestamp: "1516763973134", // Recommend this is accurate to the millisecond
        payload: {read_msgId: "TEXT_12345"}, // Read msgId is TEXT_12345
    }
```

#### Other custom message functions

* Refer to the above, set the corresponding msgType/payload

[Back to Top](#readme)

## Chat Across Applications

#### Implement chat between two different apps using the same appId/appKey/appSecret

## Push Messages

### Parameter list

| Variable            | Meanings                    |
|:------------------- |:----------------------------|
| $appId              | AppID on the Xiaomi open platform |
| $appKey             | AppKey on the Xiaomi open platform |
| $appSecret          | AppSecret on the Xiaomi open platform |
| $fromAccount      | The unique ID of the message sender in the app account system |
| $fromResource     | The identity of the message sender's device |
| $toAccount        | The unique ID of the message receiver in the app account system |
| $msgType            | msgType="base64": msg is base64 encoded data, generally used when transmitting binary data; msgType="": msg is the raw data, generally used when string data is transmitted |
| $topicId            | Topic ID |
| $packetId           | Represents the ID of the sent message packet, consists of random string plus increment ID that can be considered unique in a single appAccount |

### Push one-one-one chat information

* HTTP Request
```
    curl https://mimc.chat.xiaomi.net/api/push/p2p/ -XPOST
      -d '{"appId":$appId, "appKey":$appKey，"appSecret":$appSecret, "fromAccount":$fromAccount, "fromResource":$fromResource, "toAccount":$toAccount, "msg":$msg, "msgType":$msgType}'
      -H "Content-Type: application/json"
```

* JSON Results
```
    {
        "code":200,
        "data":{"packetId":$packetId},
        "message":"success"
    }
```

### Push group chat information

* HTTP Request
```
    curl https://mimc.chat.xiaomi.net/api/push/p2t/ -XPOST
      -d '{"appId":$appId, "appKey":$appKey，"appSecret":$appSecret, "fromAccount":$fromAccount, "fromResource":$fromResource, "msg":$msg, "topicId":$topicId, "msgType":$msgType}'
      -H "Content-Type: application/json"
```

* JSON Results
```
    {
        "code":200,
        "data":{"packetId":$packetId},
        "message":"success"
    }
```

[Back to Top](#readme)

## Message Callback

App developers can access https://admin.mimc.chat.xiaomi.net to set callback urls

### p2p instant message callback

* JSON string result in the body of post
```
    {
        "msgType":"NORMAL_MSG",
        "fromAppId":$fromAppId,
        "fromAccount":$fromAccount,
        "toAppId":$toAppId,
        "toAccount":$toAccount,
        "payload":$payload,
        "timestamp":$timestamp,
    }
```

### p2p offline message callback

* JSON string result in the body of post
```
    {
        "msgType":"OFFLINE_MSG",
        "fromAppId":$fromAppId,
        "fromAccount":$fromAccount,
        "toAppId":$toAppId,
        "toAccount":$toAccount,
        "payload":$payload,
        "timestamp":$timestamp,
    }
```

### topic instant message callback

* JSON string result in the body of post
```
    {
        "msgType":"NORMAL_TOPIC_MSG",
        "appId":$appId,
        "topicId":$topicId,
        "fromAccount":$fromAccount,
        "toAccounts":[$toAccount1,$toAccount2,...,$toAccountN], // members of topic
        "payload":$payload,
        "timestamp":$timestamp
    }
```

### topic offline message callback

* JSON string result in the body of post
```
    {
        "msgType":"OFFLINE_TOPIC_MSG",
        "appId":$appId,
        "topicId":$topicId,
        "fromAccount":$fromAccount,
        "toAccounts":[$toAccount1,$toAccount2,...,$toAccountN], // members of topic
        "payload":$payload,
        "timestamp":$timestamp
    }
```

[Back to Top](#readme)

## Group Chat Information

### Parameter list

| Variable            | Meanings                                                                                  |
|:------------------- |:----------------------------------------------------------------------------------------- |
| $appId              | AppID on the Xiaomi open platform                                                         |
| $appKey             | AppKey on the Xiaomi open platform                                                        |
| $appSecret          | AppSecret on the Xiaomi open platform                                                     |
| $topicId            | Topic ID                                                                                  |
| $topicName          | The topic name when the topic was created                                                 |
| $topicId1           | The topic ID entered by the user when querying topic ownership                            |
| $topicId2           | The topic ID entered by the user when querying topic ownership                            |
| $topicName1         | The topic name entered by the user when querying topic ownership                          |
| $topicName2         | The topic name entered by the user when querying topic ownership                          |
| $topicBulletin1     | The topic bulletin entered by the user when querying topic ownership                      |
| $topicBulletin2     | The topic bulletin entered by the user when querying topic ownership                      |
| $newBulletin        | New topic bulletins created when updating a topic                                         |
| $newTopicName       | New topic name created when updating a topic                                              |
|   $ownerUuid        | The topic owners's uuid in the MIMC account system (using user.getUuid() to obtain)       |
|   $ownerAccount     | The topic owner's unique user ID on the app account system                                |
| $ownerToken         | The owner's token (using user.getToken() to obtain)                                       |
| $userAccount1       | The unique user ID of Member 1 on the app account system                                  |
| $userAccount2       | The unique user ID of Member 2 on the app account system                                  |
| $userAccount3       | The unique user ID of Member 3 on the app account system                                  |
| $userAccount4       | The unique user ID of Member 4 on the app account system                                  |
| $userAccount5       | The unique user ID of Member 5 on the app account system                                  |
|   $userUuid1        | The uuid of userAccount1 in the MIMC account system (using user.getUuid() to obtain)      |
|   $userToken1       | The token of userAccount1 (using user.getToken() to obtain)                               |

#### Remarks:

    To obtain token, use User.getToken().
    To obtain uuid, use User.getUuid(). uuid are created by MIMC based on ($appId, $appAccount), unique.
    There are two methods of IS verification 1. token ($ownerToken/$userToken1); 2. app information, app account ($appKey, $appSecret, $ownerAccount/$userAccount1).
    When both types of verification information exist, the former is verified first. The former is generally used for the app client, and the latter is generally used for the app server. The use of these two methods is shown below.


### Create a topic

#### $ownerAccount Create Topic is Shown Below

* HTTP Request
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

* JSON Results
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

### Query information on specified topic

#### $userAccount1 Topic Information is Shown Below

* HTTP Request
```
    curl "https://mimc.chat.xiaomi.net/api/topic/$appId/$topicId"
      -H "Content-Type: application/json"
      -H "token:$userToken1"

    curl "https://mimc.chat.xiaomi.net/api/topic/$appId/$topicId"
      -H "Content-Type: application/json"
      -H "appKey:$appKey"
      -H "appSecret:$appSecret"
      -H "appAccount:$userAccount1"
```

* JSON Results
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

### Query all topic attributes

#### Information for $userAccount1 Topic Query is Shown Below

* HTTP Request
```
    curl "https://mimc.chat.xiaomi.net/api/topic/$appId/account"
      -H "Content-Type: application/json"
      -H "token:$userToken1"

    curl "https://mimc.chat.xiaomi.net/api/topic/$appId/account"
      -H "Content-Type: application/json"
      -H "appKey:$appKey"
      -H "appSecret:$appSecret"
      -H "appAccount:$userAccount1"
```

* JSON Results
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

### Invite User to Join a Topic

#### Inviting $userAccount4, $userAccount5 to join a topic is shown below

* HTTP Request
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

* JSON Results
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

### Topic member leave topic

#### $userAccount1 leaves topic is shown below

* HTTP Request
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

* JSON Results
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

* If the topic owner leaves the topic, the JSON results are shown below
```
    {"code":500,"message":"quit topic fail","data":null}
```

### Topic owner kicks member

#### $ownerAccount removes $userAccount4, $userAccount5 from the topic is shown below

* HTTP Request
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

* JSON Results
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

### Topic owner updates topic information

#### $ownerAccount updates the topic information as shown below. The topic owner is $userAccount2, the topic name is $newTopicName, the topic bulletin is $newBulletin

* HTTP Request

    curl "https://mimc.chat.xiaomi.net/api/topic/$appId/$topicId" -XPUT -d '{"topicId":$topicId, "ownerUuid":$userUuid2,"topicName":$newTopicName,"bulletin":$newBulletin}' -H "Content-Type: application/json" -H "token:$ownerToken"

    curl "https://mimc.chat.xiaomi.net/api/topic/$appId/$topicId" -XPUT -d '{"topicId":$topicId, "ownerUuid":$userUuid2,"topicName":$newTopicName,"bulletin":$newBulletin}' -H "Content-Type: application/json" -H "appKey:$appKey" -H "appSecret:$appSecret" -H "appAccount:$ownerAccount"


* JSON Results

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


### Topic owner destroys topic

#### The topic owner destroys topic is shown below

* HTTP Request

    curl "https://mimc.chat.xiaomi.net/api/topic/$appId/$topicId" -XDELETE -H "Content-Type: application/json" -H "token:$ownerToken"

    curl "https://mimc.chat.xiaomi.net/api/topic/$appId/$topicId" -XDELETE -H "Content-Type: application/json" -H "appKey:$appKey" -H "appSecret:$appSecret" -H "appAccount:$ownerAccount"


* JSON Results

    {"code":200,"message":"success！","data":null}


[Back to Top](#readme)

## Message Roaming

### Parameter list

| Variable          | Meanings                                                                     |
|:----------------- |:---------------------------------------------------------------------------- |
| $appId            | AppID on the Xiaomi open platform                                            |
| $token            | Token of the inquirer (using user.getToken() to obtain)                      |
| $account          | Unique user ID of inquirer on account system                                 |
| $fromAccount      | The message sender's unique user ID on the app account system                |
| $toAccount        | The message receivers's unique user ID on the app account system             |
| $topicId          | Topic ID                                                                     |
| $utcFromTime      | Inquiry start time. UTC time in milliseconds                                 |
| $utcToTime        | Inquiry end time. UTC time in milliseconds                                   |
| $row              | The number of message rows returned                                          |
| $messages         | The message collections returned                                             |
| $sequence         | Sequence is mainly used for message sorting and removing duplicates. Unique. |
| $payload          | The Base64 encoded message body, the app end carries out Base64 decoding     |
| $ts               | Message timestamp                                                            |

#### Remarks:

    utcFromTime and utcToTime intervals cannot exceed 24 hours, query status is [utcFromTime,utcToTime)
    Message roaming saves the user's last 6 months of message history


### Pull one-on-one chat record

    Pull the chat record for A and B

#### How to pull one-on-one chat record

* HTTP Request (POST)

    curl https://mimc.chat.xiaomi.net/api/msg/p2p/query/ -XPOST -d '{"appId":$appId,"toAccount":$toAccount,"fromAccount":$fromAccount,"utcFromTime":$utcFromTime,"utcToTime":$utcToTime}' -H "Content-Type: application/json;charset=UTF-8" -H "Accept:application/json;charset=UTF-8" -H "token:$token"


* Example of JSON Results

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
             "row": $row
         }
     }


### Pull group chat record

#### How to pull group chat record

* HTTP Request (POST)

    curl https://mimc.chat.xiaomi.net/api/msg/p2t/query/ -XPOST -d '{"appId":$appId,"account":$account,"topicId":$topicId,"utcFromTime":$utcFromTime,"utcToTime":$utcToTime}' -H "Content-Type: application/json;charset=UTF-8" -H "Accept:application/json;charset=UTF-8" -H "token:$token"


* Example of JSON Results

    {
         "code": 200,
         "message": "success",
         "data": {
             "appId": $appId,
             "topicId": $topicId,
             "row": $row,
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


[Back to Top](#readme)

## Contact Us

#### E-Mail

mimc-help@xiaomi.com

#### WeChat public account

<div align="center"><img width="200" height="200" src="https://github.com/Xiaomi-mimc/operation-manual/blob/master/img-folder/MIMC-Official-Accounts.jpg"/></div>

#### MIMC Official QQ Group

<div align="center"><img width="200" height="260" src="https://github.com/Xiaomi-mimc/operation-manual/blob/master/img-folder/MIMC-QQGroup.png"/></div>

[Back to Top](#readme)