# `MGS Server` API Guide

## Access Address

Pre-Online environment : http://pre-cpapi.mgsapi.net  
Online environment : http://cpapi.mgsapi.net

## Public request header

All of the following interfaces need to set the header information :A

`appKey` Developer platform application

`sign`  signature

**Signature rules**

The general steps for signature generation are as follows:

In the first step, let all data sent or received be set M, and sort the parameters of non-empty parameter values in set M from smallest to largest in terms of parameter name ASCII code (lexicodically), using the format of URL key-value pairs (that is, key1=value1&key2=value2...). Concatenate the string stringA.

Pay attention to the following important rules:

- Sort parameter names from smallest to largest ASCII code (lexicographical);
- If the parameter value is null, do not participate in the signature (operation);
- Parameter names are letter case sensitive;
- During the validation call, the sign parameter passed does not participate in the signature, and the generated signature is checked against the sign value.
- Currently only requests with a content-type of application/json are supported
- Lab-checking is not supported for data with parameter value of array type. It needs to be stitched into string type for stitching, and then participate in signature operation;
- Json nested types do not support tag checking. It is necessary to assemble data of nested types into Json strings and then participate in signature operation.


The second step is to get the stringSignTemp string by concatenating the key at the end of stringA, and perform MD5 operation on stringSignTemp, and then convert all characters of the obtained string to uppercase, and get the signValue of sign. Note: The key length is 32 bytes.

For example:

Sign is the signature obtained by signing the request parameters with the secret key of CP server side and the request parameters together. Every interface request will conduct data signature verification according to the secret key corresponding to the parameters and APP.

Assume that the parameters of the transmission is as follows:  {"id":"1298b012345678","name":"Recoba"}

Step 1: the parameters according to the key = value format, and sorted by parameter name ASCII dictionary sequence is as follows:  string = "name=Recoba&id=1298b012345678";

Step 2: Splice the API key:

MD5 signature method:   
stringSignTemp=stringA+"&key=$appsecret" //注：key is the applied secret key obtained from the 233 developer platform.(appsecret)

sign=MD5(stringSignTemp).toUpperCase()="10F8FA8998F16521CA6F7BCF43823A67" //note: generate capital letters of MD5 signatures


Please refer to the service side DEMO (`src/main/java/com/mgs/cloud/game/server/utils/SignUtil.java`)


# Open user interface -- only for game server


## Query user information

**Interface description**:

The interface used to verify the user's validity and obtain user information.

**Interface address**:`/api/cp/mgsCp/openUser/query`

**Request mode**:`POST`

**Request data type**:`application/json`

**Request parameter**:


```javascript
{
  "openCode": "", //user CODE
  "openId": ""  //open user ID
}
```

**Response**:

```javascript
{
	"code": 200, //Error code, 200 is success, everything else is failure
	"data": {
		"avatar": "", //user avatar
		"nickname": "", //user nickname
		"openId": ""  //user ID
	},
	"message": ""  //error note
}
```


# Room interface -- only for game server


## Create room

**Interface description**:

Optional, the Game Server can call MGS's Create Room interface to synchronize the room information after creating the room. (`PS: the client still needs to call the onCreate method again for synchronization)`

**Interface address**:`/api/cp/mgsCp/room/create`

**Request mode**:`POST`

**Request data type**:`application/json`



**Request**:


```javascript
{
  "roomIdFromCp": "", //The room ID from CP
  "roomLimit": 4, //Room member capacity
  "roomName": "", //room name
  "roomTags": ["大乱斗","5V5"] //Room label, if not, do not pass
}
```

**Response**:

```javascript
{
	"code": 200,//Error code, 200 is success, everything else is failure
	"data": {
		"memberList": [], //Room Member List
		"roomIdFromCp": "", //The room ID from CP
		"roomLimit": 0, //Room member capacity
		"roomName": "", //room name
		"roomState": 0, //Room status (value: 0 preparing, 1 playing, 2 gameover)
        "roomTags": ["大乱斗","5V5"] //Room label
    },
	"message": "" //error note
}
```


## Destroy room

**interface description**:

If the player's room is destroyed, it needs to be synchronchronized to MGS simultaneously.

**Interface address**:`/api/cp/mgsCp/room/destroy`

**Request mode**:`POST`

**Request data type**:`application/json`

**Request**:


```javascript
{
  "roomIdFromCp": "" //The room ID from CP
}
```

**Response**:

```javascript
{
	"code": 200, //Error code, 200 is success, everything else is failure
	"data": true, //Returns true on success and false on failure
	"message": ""  //error note
}
```


## Query Room Information

**interface description**:

Game side can query the basic information of the MGS room through this interface for synchronization.

**Interface address**:`/api/cp/mgsCp/room/queryInfo`

**Request mode**:`POST`

**Request data type**:`application/json`

**Request**:


```javascript
{
  "roomIdFromCp": "" //The room ID from CP
}
```

**Response**:

```javascript
{
  "code": 200,//Error code, 200 is success, everything else is failure
  "data": {
    "memberList": [], //Room Member List
    "roomIdFromCp": "", //The room ID from CP
    "roomLimit": 0, //Room member capacity
    "roomName": "", //Room name
    "roomState": 0, //Room status (value: 0 preparing, 1 playing, 2 gameover)
    "roomTags": ["大乱斗","5V5"] //Room label
  },
  "message": "" //error note
}
```


## Synchronize room information

**Interface description**:

Game side needs to synchronize the information to MGS according to the status of the room, and needs to synchronize the complete information of the room.

**Interface address**:`/api/cp/mgsCp/room/syncInfo`

**Request mode**:`POST`

**Request data type**:`application/json`

**Request**:


```javascript
{
  "roomIdFromCp": "", //The room ID from CP
  "roomLimit": 10, //Room member capacity
  "roomName": "", // Room name
  "roomState": 0, //Room status (value: 0 preparing, 1 playing, 2 gameover)
  "roomTags": ["大乱斗","5V5"] //Room label
}
```

**Response**:

```javascript
{
  "code": 200, //Error code, 200 is success, everything else is failure
  "data": true, //Returns true on success and false on failure
  "message": ""  //error note
}
```


## Synchronize room members

**Interface description**:

Game side needs to synchronize the information of members in the room to MGS, and synchronize the data of all members each time

**Interface address**:`/api/cp/mgsCp/room/syncMember`

**Request mode**:`POST`

**Request data type**:`application/json`

**Request**:

```javascript
{
  "roomIdFromCp": "", //The room ID from CP
  "openIdList": ["openId1","openId2","openId3"] //Room member capacity 
}
```

**Response example**:

```javascript
{
  "code": 200, //Error code, 200 is success, everything else is failure
  "data": true, //Returns true on success and false on failure
  "message": ""  //error note
}
```

