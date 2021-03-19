# `MGS服务端` API文档

## 访问地址
预发环境 : http://pre-cpapi.mgsapi.net  
线上环境 : http://cpapi.mgsapi.net

## 公共请求头

以下所有接口都需要设置header信息:

`appKey` 开发者平台申请

`sign`  签名

**签名规则**

签名生成的通用步骤如下：

第一步，设所有发送或者接收到的数据为集合M，将集合M内非空参数值的参数按照参数名ASCII码从小到大排序（字典序），使用URL键值对的格式（即key1=value1&key2=value2…）拼接成字符串stringA。

特别注意以下重要规则：

- **参数名ASCII码从小到大排序（字典序）** ；
- **如果参数值为空不参与签名（运算）** ；
- **参数名称大小写敏感（【参数名区分大小写】** ；
- **验证调用时，传送的sign参数不参与签名，将生成的签名与该sign值作校验** 。
- 参数值为数组类型数据且元素不存在null和嵌套json类型支持签名（空数组也需要签名）
    - 注意数组是有序的，接入方签名时候的顺序和提交到MGS服务器端的数组顺序需要保持一致，注意不要使用普通的Dictionary或Map，需要是有序；
    - 数组元素中不能存在null值；
    - 数组中元素不能存在嵌套JSON类型；
    - 数组中如果存在元素嵌套类型，需要将整个数组元素嵌套类型转换为String去标识JSON对象再参与签名运算；否则会返回错误信息"参数不合法"

- Json嵌套类型不支持toString()直接验签，需要将嵌套类型的数据组装为Json字符串，再参与签名运算；


第二步，在stringA最后拼接上key得到stringSignTemp字符串，并对stringSignTemp进行MD5运算，再将得到的字符串所有字符转换为大写，得到sign值signValue。

注：为了防止因为简单接口参数或者入参较为固定，推荐开发接入时，可以在参数体中增加一个nonce为参数key，value为时间戳或者随机字符串（参与参数签名），增加签名value的随机性，防止网络劫持带来的秘钥泄露；【非强制】



举例：

SIGN 为请求参数的通过CP服务器端秘钥和请求参数一起进行签名运算得到的签名，每一次接口请求都会根据参数和APP对应的秘钥进行数据签名验证；

假设传送的参数如下： {"id":"1298b012345678","name":"Recoba"}

第一步：对参数按照key=value的格式，并按照参数名ASCII字典序排序如下： string = "name=Recoba&id=1298b012345678";

第二步：拼接API密钥：

MD5签名方式：

```java

stringSignTemp=stringA+"&key=$appsecret" //注：key为在233开发者平台申请获得的应用秘钥(appsecret)   

sign=MD5(stringSignTemp).toUpperCase()="10F8FA8998F16521CA6F7BCF43823A67" //注：生成大写的MD5签名方式  

``` 

请参考服务端DEMO(`src/main/java/com/mgs/cloud/game/server/utils/SignUtil.java`)


# 开放用户接口--游戏服务器专用

## 查询用户信息

**接口描述**:

用于验证用户有效性以及获取用户信息的接口。

**接口地址**:`/api/cp/mgsCp/openUser/query`


**请求方式**:`POST`


**请求数据类型**:`application/json`


**请求参数**:

```javascript
{
  "openCode": "", //用户CODE
  "openId": ""  //开放用户ID
}
``` 


**响应**:
```javascript
{
	"code": 200, //错误码, 200是成功，其他都为失败
	"data": {
		"avatar": "", //用户头像
		"nickname": "", //用户昵称
		"openId": ""  //用户ID
	},
	"message": ""  //错误描述
}
```


# 房间接口--游戏服务器专用


## 创建房间


**接口描述**:

可选,游戏服务端可以在创建房间后调用MGS的创建房间接口，同步房间信息。


**接口地址**:`/api/cp/mgsCp/room/create`


**请求方式**:`POST`


**请求数据类型**:`application/json`


**请求**:

```javascript
{
  "roomIdFromCp": "111dddd", //来自cp的房间ID,必传
  "roomLimit": 4, //房间成员容量
  "roomName": "", //房间名称
  "roomTags": ["大乱斗","5V5"] //房间标签,没有则不传
  "canVoice": true, // 是否使用开麦,默认开启
  "canRoomChat": true // 是否使用聊天室,默认开启
}
```

**响应**:

```javascript
{
	"code": 200,//错误码, 200是成功，其他都为失败
	"data": {
		"memberList": [], //房间成员列表
		"roomIdFromCp": "111dddd", //来自cp的房间ID
		"roomLimit": 0, //房间成员容量
		"roomName": "今天天气不错", //房间名称
        "roomShowNum": "100123", //房间显示号
		"roomState": 0, //房间状态(取值：0 准备中,1 游戏中,2 游戏结束)
        "roomTags": ["大乱斗","5V5"], //房间标签
        "canVoice": true, // 是否使用开麦
        "canRoomChat": true // 是否使用聊天室
    },
	"message": "" //错误描述
}
```


## 销毁房间

**接口描述**:

游戏方房间若销毁了，需要同步给MGS进行房间销毁。


**接口地址**:`/api/cp/mgsCp/room/destroy`


**请求方式**:`POST`


**请求数据类型**:`application/json`


**请求**:

```javascript
{
  "roomIdFromCp": "" //来自cp的房间ID,必传
}
``` 

**响应**:

```javascript
{
	"code": 200, //错误码, 200是成功，其他都为失败
	"data": true, //成功返回true，失败返回false
	"message": ""  //错误描述
}
```


## 查询房间信息


**接口描述**:

游戏方可通过该接口查询MGS房间的基本信息进行同步。

**接口地址**:`/api/cp/mgsCp/room/queryInfo`


**请求方式**:`POST`


**请求数据类型**:`application/json`


**请求**:


```javascript
{
  "roomIdFromCp": "" //来自cp的房间ID,必传
}
```


**响应**:

```javascript
{
  "code": 200,//错误码, 200是成功，其他都为失败
  "data": {
    "memberList": [], //房间成员列表
    "roomIdFromCp": "", //来自cp的房间ID
    "roomLimit": 0, //房间成员容量
    "roomName": "", //房间名称
    "roomShowNum": "100123", //房间显示号
    "roomState": 0, //房间状态(取值：0 准备中,1 游戏中,2 游戏结束)
    "roomTags": ["大乱斗","5V5"], //房间标签
    "canVoice": true, // 是否使用开麦
    "canRoomChat": true // 是否使用聊天室
  },
  "message": "" //错误描述
}
```




## 同步房间信息


**接口描述**:


游戏方需要根据房间内的状态将信息同步给MGS,建议同步房间的完整信息,某些字段不传则不修改


**接口地址**:`/api/cp/mgsCp/room/syncInfo`


**请求方式**:`POST`


**请求数据类型**:`application/json`


**请求**:


```javascript
{
  "roomIdFromCp": "", //来自cp的房间ID,必传
  "roomLimit": 10, //房间成员容量
  "roomName": "", // 房间名称
  "roomState": 0, //房间状态:0准备中,1游戏中,2游戏结束
  "roomTags": ["大乱斗","5V5"] //房间标签，数组
}
```

**响应**:

```javascript
{
  "code": 200, //错误码, 200是成功，其他都为失败
  "data": true, //成功返回true，失败返回false
  "message": ""  //错误描述
}
```


## 同步房间成员


**接口描述**:

可选，游戏方需要根据房间内成员信息同步给MGS,每次同步全员数据


**接口地址**:`/api/cp/mgsCp/room/syncMember`


**请求方式**:`POST`


**请求数据类型**:`application/json`


**请求**:

```javascript
{
  "roomIdFromCp": "", //来自cp的房间ID,必传
  "openIdList": ["openId1","openId2","openId3"] //房间成员容量 
}
``` 


**响应**:

```javascript
{
  "code": 200, //错误码, 200是成功，其他都为失败
  "data": true, //成功返回true，失败返回false
  "message": ""  //错误描述
}
```
