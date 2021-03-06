# Cloud Development Manual


## **Home**

### **Overview**

AIOT is a cloud-based IOT platform that provides services such as user management, device management, data collection, and remote control. The data collected by the smart device is stored in the AIOT cloud database. Examples of data collected include power, temperature and humidity, switch status, and the status of windows and doors. At the same time, the AIOT cloud can remotely control devices by issuing control commands. How do developers obtain the data that was collected? Or how to remotely control their own equipment?

The AIOT Open Cloud Platform provides a cloud-based connection interface to meet the needs of third-party developers. Through Open APIs and messaging services, developers can create rich IoT applications by collecting data and remotely controlling devices via the cloud.

The AIOT Open Cloud Platform provides a comprehensive interface, including:

- User Management

- Location Management

- Device Management

- Scenario Management

- Resource Management

- Linkage Management

  ​

In addition, the AIOT Open Cloud Platform provides a push notification service, which supports pushing the real-time data reported by the device to a third-party server, meeting developers' requirements for receiving data in real-time.

In the following, this manual describes how to develop third-party applications that utilizes cloud-based connection interfaces. This consists of application creation, configuration, and development.

### **Preface**

A good software development process on a good software system architecture will greatly reduce the software development effort needed. Therefore, in order to help developers enjoy a smooth development process, we will first go over some important issues. If you are an experienced developer, you can skip this section.

Before formally starting the application development process, developers need to complete some preparatory work. Please refer to the following process.

![应用开发流程](http://cdn.cnbj2.fds.api.mi-img.com/cdn/aiot/doc-images/en/development/doc-cloud-development/application-development-process.png)

A stable and efficient application needs to consider API call performance, message processing performance, high availability of Access-Token, etc. We suggest referencing the following proposed architecture.

![应用参考架构图](http://cdn.cnbj2.fds.api.mi-img.com/cdn/aiot/doc-images/en/development/doc-cloud-development/application-reference-architecture.png)

If the developer is not familiar with the use of API call methods, we recommended the use of a Web API test tool: [Postman](https://www.getpostman.com/). Postman is an excellent Web API and HTTP request debugging tool. It is powerful, has an intuitive interface, and is easy to use. Postman is a tool that helps developers test AIOT Open APIs. During this process, developers can get a quick grasp of how APIs are invoked.

> **Note**: Turn off SSL certificate verification when using Postman. 

## **Application creation and configuration**

### **Creating the application**

Login to the [AIOT Open Cloud Platform](https://opencloud.aqara.cn/) and click on "Create New Application". When creating an application, the developer needs to configure the relevant parameters for the application, including:

- Application Name
- Industry
- Introduction

After creating an application, the platform automatically assigns the AppID and AppKey. The AppID is the unique identifier of the application and the AppKey is the application's secret key. AppID and AppKey are very important and are frequently used during application development process. For example, when calling the AIOT Open Cloud Platform Open API, developers need to add AppID and AppKey as validation parameters in the request header.

> **Note**: Developers must take proper care of their AppKey, prevent disclosure to unauthorized parties, and reset their AppKey on a regular basis.

### **Apply for resource permissions**

Resource is an abstract term. In this present scenario, the data generated by the device, including the setting parameters and real-time status of the device, are all resources. A device has multiple resources, different resources represent different types of information represented by the data. For example, the switch status (plug_status) is a smart socket resource used for indicating whether the smart socket is currently powered on. By accessing this resource, developers can query for the current status of sockets and remotely switch sockets on or off.

Before accessing a resource, developers need to request permission based on access level. There are two resource access levels, level 1 and level 2. Level 1 represents commonly accessed resources and level 2 represents less commonly accessed resources. When level 1 access is granted to a developer for a type of device, the developer can use all the commonly accessed resources for that type of device in their application. By default, the APP is automatically assigned level 1 access permissions for all types of devices.

If developers need to apply for level 2 access permissions, please visit the "Application Management" page and change to the "Resource Authorization" page, as shown in the following figure.

- Click the "Apply Resource" button on the right side of each row to apply for resources corresponding to the type of device.
- Select multiple check boxes on the left, and then click the "Batch Application" button in the upper right corner. You can apply for resources corresponding to multiple device types in batches.

You need to wait for 1 to 3 business days after submitting the application form. Developers will be notified of the application result by SMS or email.

![资源授权页](http://cdn.cnbj2.fds.api.mi-img.com/cdn/aiot/doc-images/en/development/doc-cloud-development/apply-resource.png)

### **Apply for API permissions**

APIs are not only used by the AIOT Open Cloud Platform to provide an external data interface, but also the main method for developers to query and control equipment. Before developing applications, developers should apply for the API permissions based on their own needs. By default, APPs are automatically assigned common API permissions.

If a developer needs to request access to an API, please visit the "Application Management" page and switch to the "API List" page, as shown in the following figure.

- Click the "Apply" button to the right of each row to request access to the API.
- Click on the "Batch Application" button in the upper right corner and apply for access to multiple APIs using the batch application feature.

You need to wait for 1 to 3 business days after submitting the application form. Developers will be notified of the application result by SMS or email.

![API访问页](http://cdn.cnbj2.fds.api.mi-img.com/cdn/aiot/doc-images/en/development/doc-cloud-development/apply-api.png)

## **Account authorization**

After the device is online, the device is linked to a unique AIOT account, the login account of the Aqara APP. Third-party applications can access and control the devices under the AIOT account only after being authorized by the AIOT account. At the same time, the AIOT platform can push device messages to third-party servers.

> **Note**: Search for "Aqara" in the App Store or Apple Store, download and install, then register the Aaqra App Account.

Currently, the AIOT Open Cloud Platform only provides one authorization method: OAuth 2.0. Additional authorization methods will be provided in the future.

### **OAuth2.0**

OAuth2.0 is an open standard that allows users to grant third-party applications access to private resources (such as user information, photos, videos, device data, etc.) stored by users on websites (or Internet of Things platform) without having to provide their username and password to third-party applications. If you want to read more about the OAuth2.0 open standard, please refer to [Understanding OAuth2.0](http://www.ruanyifeng.com/blog/2014/05/oauth_2_0.html) | [OAuth standard (English)](https://oauth.net/2/).

AIOT Open Cloud Platform implements the OAuth 2.0 standard authorization code (authorization_code) mode, for server-side applications. The OAuth 2.0 authorization process is simple and secure. The time-process diagram is shown below. An access token (token) is obtained after successfully completing the authorization process. After receiving access tokens, developers can use access tokens to gain access to interfaces, get user information, or manipulate users' devices.

![Oauth2.0授权码模式时序图](http://cdn.cnbj2.fds.api.mi-img.com/cdn/aiot/doc-images/en/development/doc-cloud-development/auth-sequence-diagram.png)

Detailed OAuth 2.0 authorization process is as follows:

> **Note:** The URL in this chapter takes “Mainland China( https://aiot-oauth2.aqara.cn/)” as an example, other area please refer to [Country (area code)](http://docs.opencloud.aqara.cn/en/development/region_code) .

#### **Step 1 Request authorization code**

First, third-party applications need to redirect users to the AIOT OAuth 2.0 service through a web browser. After logging in successfully using **the Aqara APP account**, the AIOT Open Cloud Platform returns the user's authorization code. The validity period of the authorization code is 10 minutes. Please complete the following process within 10 minutes.

- **URL：**  https://aiot-oauth2.aqara.cn/authorize?client_id=xxx&response_type=code&redirect_uri=xxxx&state=xxx&theme=x
- **Request method:**  HTTP GET
- **Request parameters**

| **Parameter name** | **Is required?** | **Description**                          |
| ------------------ | ---------------- | ---------------------------------------- |
| client_id          | Yes              | Third-party application ID, AppID        |
| response_type      | Yes              | Return type, according to the OAuth 2.0 standard, get the value of the `code` |
| redirect_uri       | Yes              | Third-party application registration redirect URI |
| state              | No               | The value is an arbitrary string, and the authentication server will return the original value in the parameter |
| theme              | No               | Page theme, currently supports themes 0, 1, and 2, a total of three themes. The default theme 1 |

- **Return to instructions**
```
GET  HTTP/1.1 302 Found
Location: https://redirect_uri?code=xxx&state=xxx
```

#### **Step 2 Obtaining the access token**

After obtaining the authorization code, the third-party application accesses the following URL, using the authorization code in exchange for obtaining the access token.

- **URL：** https://aiot-oauth2.aqara.cn/access_token
- **Request method:**  HTTP POST (application/x-www-form-urlencoded)
- **Request parameters**

| **Parameter name** | **Is required?** | **Description**                          |
| ------------------ | ---------------- | ---------------------------------------- |
| client_id          | Yes              | Third-party application ID, AppID        |
| client_secret      | Yes              | Third-party application key, AppKey      |
| grant_type         | Yes              | According to the OAuth 2.0 standard, the value of `authorization_code` is obtained |
| code               | Yes              | The authorization code obtained during the previous step |
| redirect_uri       | Yes              | The redirect_uri parameter configured in the previous request |

- **Return Example (Status Code 200)**
```
{
    "access_token": "xxxxx", 
    "expires_in": 7200, 
    "token_type": "bearer", 
    "openId": "xxx", 
    "refresh_token": "xxxxx", 
    "state": "abcde"
}
```
- **Return parameter**

| **Parameter name** | **Description**                          |
| ------------------ | ---------------------------------------- |
| access_token       | Access token, used to grant third-party applications access to AIOT Open Service credentials |
| expires_in         | The remaining time that the access token is valid, in seconds |
| token_type         | According to the OAuth 2.0 standard, the value of `bearer` is obtained |
| openId             | Authorized user's unique identifier      |
| refresh_token      | Refresh token, used to refresh the access token, valid for 30 days |
| state              | The value is an arbitrary string, and the authentication server will return the original value in the parameter |

#### **Step 3 Refreshing the access token**

Since the access token is valid for only 2 hours, the developer needs to use a refresh token to refresh the access token before the access token expires. The recommended refresh interval is 1.5 hours (1 hour and 30 minutes).

- **URL：** https://aiot-oauth2.aqara.cn/refresh_token
- **Request method:**   HTTP POST (application/x-www-form-urlencoded)
- **Request parameters**

| **Parameter name** | **Is required?** | **Description**                          |
| ------------------ | ---------------- | ---------------------------------------- |
| client_id          | Yes              | Third-party application ID, AppID        |
| client_secret      | Yes              | Third-party application key, AppKey      |
| grant_type         | Yes              | According to OAuth 2.0 standard, the value of `refresh_token` is obtained |
| refresh_token      | Yes              | The refresh token obtained during the previous step |

- **Return Example (Status Code 200)**
```
{
    "access_token": "xxxxx", 
    "expires_in": 7200, 
    "token_type": "bearer", 
    "openId": "xxx", 
    "refresh_token": "xxxxx", 
    "state": "abcde"
}
```
- **Return parameter**

| **Parameter name** | **Description**                          |
| ------------------ | ---------------------------------------- |
| access_token       | Access token, used to grant third-party applications access to AIOT Open Service credentials |
| expires_in         | The remaining time that the access token is valid, in seconds |
| token_type         | According to the OAuth 2.0 standard, the value of `bearer` is obtained |
| openId             | Authorized user's unique identifier      |
| refresh_token      | New refresh token, old refresh token becomes void immediately |
| state              | The value is an arbitrary string, and the authentication server will return the original value in the parameter |

> **Note**: Please try again if anomalies occur while refreshing the access token!

## **API call**

### **API call specification**

1. To ensure the security of data transmissions, the API provided by the AIOT Open Cloud Platform transfers data over the HTTPS protocol. The unified domain name of Mainland China is **https://aiot-open-3rd.aqara.cn**, other area please refer to [Country (area code)](http://docs.opencloud.aqara.cn/en/development/region_code) .
2. OpenID is a third-party application's unique user identifier. It is the result of encrypting the original AIOT account. Each AIOT user is issued a unique OpenID for each third-party application.
3. The body of the request and the results returned through the interface using the **JSON format**. If the developer uses another format, the "request parameter error" will be returned.
4. When querying the device status or controlling the device through the interface, you need to configure the "resource alias" parameter. The values for different resources are different. For information on all resources (alias, value type, meaning, etc.), please visit the [AIOT Open Cloud Platform](https://opencloud.aqara.cn/) "Application Management -> Resource Authorization" page.
5. For detailed API definitions of all functions, please visit the "Application Management -> API Access" page in the [AIOT Open Cloud Platform](https://opencloud.aqara.cn/).

### **Call example**

For example, you can query the details of a device by calling the interface using the following methods:

- Request URL：https://aiot-open-3rd.aqara.cn/open/device/query
- Request mothod:  HTTP POST （application/json）
- Request header example

| **Key**      | **Value**                        | **Description (optional)**               |
| ------------ | -------------------------------- | ---------------------------------------- |
| Appid        | 54a230103556040223478911         | The application's unique identifier      |
| Appkey       | oT7kp77vzwxBn7siiXISamsPpvaTaWeZ | Application's Secret Key                 |
| Openid       | Yb2bR2btJC6L8EmU5Z3jzh4oQsttie   | User ID obtained via OAuth authorization |
| Access-Token | 12db5a10c49fd289963dbc67a0d13ab5 | Access token obtained via OAuth authorization |
| Content-Type | application/json                 | The result is returned in JSON format    |

> **Note**: Make sure the Keys and the corresponding Values are filled in request header. Note that they are case sensitive.

- Request body example

```
  {
  "openId": "Yb2bR2btJC6L8EmU5Z3jzh4oQsttie",
  "did": "lumi.158d00013fd654"
  }
```
> **Note**: The parameter "did" indicates the device ID, which is the unique identifier of the device. Developers can call the interface to obtain all device "did" for an OpenID.

- Returning results example
```
  {
    "result": {
        "bindDate": "2017-11-13",
        "chipVersion": "",
        "bindTime": "22:35:18",
        "name": "Bedroom-Motion Sensor",
        "model": "lumi.sensor_motion.es2",
        "isOnline": 1,
        "firmwareVersion": "1",
        "did": "lumi.158d00013fd654",
        "parentId": "lumi.158d00010d65a9"
    },
    "code": 0,
    "isBytesData": 0,
    "requestId": "oHjXRtRdnm"
  }
```

## **Push notification**

Developers need to follow these steps to enable the push notification feature if third-party applications need to receive device messages:

1. Fill in the server configuration;
2. Authentication service server address;
3. Subscribe to notification by message type;
4. Implement business logic based on message format.

Special attention should be given to ensure third-party applications receiving notifications must generally return confirmation of recipient in accordance with the provisions of the JSON message format. The format is as follows:

```
{
	"code": 0|ErrorCode,
	"result": "Custom content"
}
```

### **Server configuration**

Open the "Application Management -> Push Notification" page, click the "Edit" button on the upper right corner, fill in the configuration information:

1. **URL**: The server address, the interface URL used by the third-party server to receive the notification;
2. **Token**: arbitrary filled in by the developer, used to generate signatures;
3. **EncodingAESKey**: Randomly generated or manually filled in by the developer, will be used to decrypt the message body;
4. **Message encryption and decryption methods**: consists of clear text mode, compatibility mode, and secure mode. Changes to the message encryption and decryption mode will take effect immediately. Developers need to be very careful when making modifications.

Detailed descriptions of differences between the message encryption and decryption methods:

- Plain Text mode: Does not encrypt the message body, low safety factor;
- Compatibility mode: The message body contains both plaintext and ciphertext to facilitate debugging and maintenance by developers;
- Safe mode: The message body is pure ciphertext, requires developers to encrypt and decrypt, has a high safety factor.

> **Note**: Presently, only the "plain text mode" is supported. Developers are instructed to use clear text mode.

![消息服务器配置](http://cdn.cnbj2.fds.api.mi-img.com/cdn/aiot/doc-images/en/development/doc-cloud-development/message-subscribe.png)

### **Authentication server**

**Plain Text mode**

In plain text mode, the server authentication method is very simple. After the developer saves the server configuration, the AIOT server sends a POST request to the server address (URL) as configured by the developer. The request will carry an "echostr" parameter(JSON format) that consists of a random string. If the third-party server receives the request, please return the "echostr" parameter without any changes to ensure authentication to the server is successful; otherwise, the authentication fails.

Format for sending messages:

```
{
	"echostr": "jdlfialjf8i"
}
```

The format for returning messages is as follows:

```
{
	"code": 0,
	"result": "jdlfialjf8i"
}
```

**Safe mode**

Under safe mode, the server authentication method becomes complicated, and similar to the WeChat Public Platform.  After the developer saves the server configuration, the AIOT server will send a GET request to the  server address (URL) with the following parameters:

- **signature**: encryption signature. The signature consists of the "Token" parameter filled in by the developer with the "timestamp" parameter for the request, and the "nonce" parameter;
- **timestamp**: timestamp
- **nonce**: random number;
- **echostr**: random string.

If it is confirmed that the GET request is from the LUMI Server, please return the content of the echostr parameter as it is to confirm authenticating to the server was successful; otherwise, the authentication fails. The encryption/verification process during authentication is as follows:

1. Arrange the "Token", "timestamp", and "nonce" parameters in dictionary order;
2. Then concatenate the three parameters into a string and apply sha1 encryption;
3. Developers receiving the encrypted string can compare it with the signature, identifying the request source from the AIOT server.

### **Message format**

At present, the AIOT Open Cloud Platform supports the following two types of messages:

- Resource Message: resource change information, such as temperature changes, power changes, etc .
- Device Messages: Device event messages, such as device on-line/off-line, linking/unlinking, etc.

**Resource Message**

```
{
    "msgType": "resource", 
    "data": [
        {
            "time": "1503556533", 
            "attr": "load_power", 
            "value": "3.93", 
            "did": "lumi.158d00011c1cee"
        }
    ]
}
```

| **Parameters** | **Description**                          |
| -------------- | ---------------------------------------- |
| msgType        | Message type, gets the value of `resource` |
| time           | Timestamps, units: seconds               |
| attr           | Resources aliases. Please refer to the "Application Management -> Resource Authorization" page for more details regarding each resource. |
| value          | The latest value of resources            |
| did            | Device ID                                |

**Device Message**

```
{
    "msgType": "device", 
    "data": {
        "openId": "GoeFrrL7mN9SsGRi8WJn4x4YnQpXTS", 
        "name": "Air Conditioning Controller", 
        "model": "lumi.acpartner.aq1", 
        "time": 1503560767, 
        "event": "DEV_INFO_CHANGED", 
        "did": "lumi.158d00010b4090", 
        "parentId": "", 
        "extra": "{"clientId":"xxxx"}"
    }
}
```

| **Parameters** | **Description**                          |
| -------------- | ---------------------------------------- |
| msgType        | Message type, gets the value of `device` |
| openId         | User ID                                  |
| name           | Device Name                              |
| model          | Device Type                              |
| time           | Timestamps, units: seconds               |
| event          | Event type, please refer to the detailed parameter description in the following table |
| did            | Device ID                                |
| parentId       | Parent device (gateway) ID. If this is a gateway, this field is left blank |

| **Event type**   | **Description**                |
| ---------------- | ------------------------------ |
| GW_BIND          | Link to Gateway                |
| GW_UN_BIND       | Unlink Gateway                 |
| GW_ONLINE        | Gateway is on-line             |
| GW_OFFLINE       | Gateway is off-line            |
| SUB_DEV_BIND     | Link sub-device to network     |
| SUB_DEV_UN_BIND  | Unlink sub-device from network |
| SUB_DEV_ONLINE   | Sub-device is on-line          |
| SUB_DEV_OFFLINE  | Sub-device is off-line         |
| DEV_INFO_CHANGED | Device information changes     |

## **Description of Return codes**

Each time a third-party application calls an interface, it may get a return code indicating it is correct or incorrect. Developers can debug the interface based on the return code information and troubleshoot the error.

| **Category**  | **Return code** | **Status**                               | **Description**                          |
| ------------- | --------------- | ---------------------------------------- | ---------------------------------------- |
| SUCCESS       | 0               | SUCCESS                                  | Success                                  |
| ERROR_PACKAGE | 100             | ERROR_TIMEOUT                            | Timeout, time out                        |
| ERROR_PACKAGE | 101             | ERROR_PACKAGE_ILLEGAL                    | Illegal packets                          |
| ERROR_PACKAGE | 102             | ERROR_PACKAGE_DAMAGE                     | Packet is corrupted                      |
| ERROR_REQUEST | 301             | ERROR_REQUEST_PATH                       | Request path error                       |
| ERROR_REQUEST | 302             | ERROR_REQUEST_PARAMS                     | Request parameter error                  |
| ERROR_USER    | 401             | ERROR_USER_NO_REG                        | User not registered                      |
| ERROR_USER    | 402             | ERROR_USER_NO_LOGIN                      | User is not logged in                    |
| ERROR_USER    | 403             | ERROR_USER_PERMISSION_DENIED             | Deny access to user, insufficient permissions |
| ERROR_USER    | 411             | ERROR_PASSWORD_NOT_CORRECT               | Password error                           |
| ERROR_USER    | 412             | ERROR_TOKEN_FAILED                       | Token failure                            |
| ERROR_SERVER  | 500             | ERROR_INTERNAL_SERVER                    | Server Error, error during server process |
| ERROR_DEVICE  | 601             | ERROR_DEVICE_NO_REG                      | Device is not registered                 |
| ERROR_DEVICE  | 602             | ERROR_DEVICE_OFFLINE                     | Device is offline                        |
| ERROR_DEVICE  | 603             | ERROR_DEVICE_PERMISSION_DENIED           | Access denied, insufficient permissions. |
| ERROR_DEVICE  | 604             | ERROR_DEVICE_BIND                        | Device linking error, not linked, or linked user name is incorrect |
| ERROR_THIRD   | 801             | ERROR_APP3RD_APPID_OR_APPKEY_ILLEGAL     | AppID or AppKey is incorrect             |
| ERROR_THIRD   | 802             | ERROR_THIRD_APP_ID_HAS_NO_PERMISSION     | The AppID does not have permission to access this API |
| ERROR_THIRD   | 805             | ERROR_APP3RD_OAUTH2_ACCESSTOKEN_ILLEGAL  | AccessToken error                        |
| ERROR_THIRD   | 806             | ERROR_APP3RD_OAUTH2_ACCESSTOKEN_EXPIRED  | AccessToken expired                      |
| ERROR_THIRD   | 807             | ERROR_APP3RD_OAUTH2_REFRESHTOKEN_ILLEGAL | RefreshToken error                       |
| ERROR_THIRD   | 808             | ERROR_APP3RD_OAUTH2_REFRESHTOKEN_EXPIRED | RefreshToken expired                     |
| ERROR_OTA     | 901             | ERROR_OTA_FIRMWARE_NOT_EXIST             | The Firmware selected does not exist     |

## Resource definition

Please go to "resource permission" page to get all resource of cloud development, below is a definition of some special resoures.

Resource: **ac_state**

Compression mode of air conditioning command (4 bytes):  (binary)

|0 1 2 3|4 5 6 7|8 9 10 11|12 13 14 15|16 17 18 19 20 21 22 23|24 25 26 27 28 29 30 31|

| Position  | Data                                     | Description           |
| --------- | ---------------------------------------- | --------------------- |
| [0 ~3]    | 0: off; 1: on; 2: toggle; E: circle; F: invalid; else: reserve | switch                |
| [4 ~ 7]   | 0: heat; 1: cool; 2: auto; 3: dry; 4: wind; E: circle; F: invalid; else: reserve | mode                  |
| [8 ~ 11]  | 0: low; 1: middle; 2: high; 3: auto; E: circle; F: invalid; else: reserve | speed                 |
| [12 ~ 13] | 0: horizontal; 1: vertical; 2: circle; 3: invalid; | wind direction        |
| [14 ~ 15] | 0: swing; 1: fix; 2: circle; 3: invalid; | sweeping              |
| [16 ~ 23] | 0 ~ 240; 243: up; 244: down; FF: invalid | temperature           |
| [24]      | default: 0                               | extension digit       |
| [25]      | default: 0                               | compression code      |
| [26]      | default: 0                               | LED display           |
| [27]      | 0: switch command; 1: non-switching command | switch command        |
| [28 ~ 31] | 00: stateless; 01: stateful; 02: protocal; 03: recommended scenario; 04: semi-state; 11: ignore | air conditioning type |

> Note:
>
> - [16-23] The temperature value (0~244) is decimal. If temperature is 25 degrees, then binary is 00011001.
> - The value of "ac_state" must be decimal.
> - Mode, speed and wind direction should not be F(invaild), it will probably control failure because the value cannot be recognized. Before setting the value of ac_state, it is better to query the current value of ac_state by API(/open/res/query/multi/option/extended), then change the value according to the requirement.

For example: "open air conditioning, mode is cool, speed is low, wind direction is horizontal, sweeping is swing and temperature is 25 degrees". 
According to the above-metioned, binary is 00010001000000000001100100000001, convert binary to decimal, then the value of ac_state is “285219073”.