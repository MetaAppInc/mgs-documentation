# `MGS Server` API Guide

## Access Address

Online environment : https://cpapi.mgsapi.net

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

- The parameter value is array type data and the element does not exist null and the nested json type supports signatures (empty arrays also need to be signed)

  - Note that the array is in order. The order of the signature of the access party and the order of the array submitted to the MGS server must be the same. Be careful not to use ordinary Dictionary or Map. It needs to be in order;

  - No null value can exist in the array element；
  - The elements in the array cannot have nested JSON types；
  - If there is a nested type of elements in the array, you need to convert the nested type of the entire array element to String to identify the JSON object and then participate in the signature operation; otherwise, the error message "The parameter is invalid" will be returned.

- The Json nested type does not support toString() to directly verify the signature. It is necessary to assemble the data of the nested type into a Json string, and then participate in the signature operation;

The second step is to get the stringSignTemp string by concatenating the key at the end of stringA, and perform MD5 operation on stringSignTemp, and then convert all characters of the obtained string to uppercase, and get the signValue of sign. 

Note: In order to prevent the simple interface parameters or the input parameters from being relatively fixed, it is recommended to add a nonce to the parameter body as the parameter key, and the value as a timestamp or a random string (participating in parameter signatures) to increase the randomness of the signature value when developing access. , To prevent the disclosure of secret keys caused by network hijacking; [non-mandatory]

For example:

Sign is the signature obtained by signing the request parameters with the secret key of CP server side and the request parameters together. Every interface request will conduct data signature verification according to the secret key corresponding to the parameters and APP.

Assume that the parameters of the transmission is as follows:  {"id":"1298b012345678","name":"Recoba"} 

Step 1: the parameters according to the key = value format, and sorted by parameter name ASCII dictionary sequence is as follows:  string = "name=Recoba&id=1298b012345678"; 

Step 2: Splice the API key:  

MD5 signature method:   

```
stringSignTemp=stringA+"&key=$appsecret" //Note: key is the application key obtained by applying for the 233 developer platform(appsecret)   

sign=MD5(stringSignTemp).toUpperCase()="10F8FA8998F16521CA6F7BCF43823A67" //Note: Generate uppercase MD5 signature method
```

Please refer to the service side DEMO (`src/main/java/com/mgs/cloud/game/server/utils/SignUtil.java`)



# Server error code

| Error type                                                   | error code(code) |                    error message(message)                    |
| ------------------------------------------------------------ | ---------------- | :----------------------------------------------------------: |
| Normal                                                       | 200              |                           Success                            |
| Parameter error                                              | 400              | Incoming parameter error The name cannot be empty The length of the name cannot be greater than [N] characters The name is illegal The openCode is incorrect |
| System exception                                             | 500              | The system is abnormal, please try again later or contact customer service |
| Authentication error                                         | 401              |                     Authentication error                     |
| Data does not exist                                          | 1016             |          Data does not exist or User does not exist          |
| Business exception                                           | 1099             |         The game is illegal, the user does not exist         |
| Business exception                                           | 80001            |                     Room does not exist                      |
| Business exception                                           | 80002            |                        Room destroyed                        |
| Business exception                                           | 80003            |                         Room is full                         |
| Business exception                                           | 80004            |             Operation not allowed by non-members             |
| Gateway service abnormal                                     | 25002            |                   Service system abnormal                    |
| Gateway service abnormal                                     | 25003            |                      Business exception                      |
| Gateway verification request parameter is invalid            | 25004            |                      Invalid parameter                       |
| Gateway verification request parameter is empty or missing   | 25005            |                      Missing parameters                      |
| Gateway verification request header parameter is missing     | 25006            |                   Illegal parameter header                   |
| The gateway cannot find the APP information according to the AppKey | 25007            |                   Illegal parameter header                   |
| The content-type of the request processed by the gateway is not accepted for processing | 25008            |                 Illegal request content-type                 |
| The APP obtained by the gateway according to the AppKey is disabled and unavailable | 25050            |                   Account is not available                   |
| The gateway cannot find the APP information according to the AppKey | 25051            |                   Account is not available                   |
| Gateway verification request verification is incorrect       | 25052            |                Signature verification failed                 |


# User interface -- only for game server

| Interface              | Description                                                  |
| ---------------------- | ------------------------------------------------------------ |
| Query user information | Interface used to verify user validity and obtain user information |

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

# Friend interface -- only for game server

| Interface                 | Description                                 |
| ------------------------- | ------------------------------------------- |
| If the player is a friend | Used to verify the friendship between users |

## If the player is friend

**Interface description**:

Used to verify the friendship between users, support batch, support up to 50 users to verify at the same time

**Interface address**:`/api/cp/mgsCp/friend/isFriendByBatch`

**Request mode**:`POST`

**Request data type**::`application/json`

**Request parameter**:

```
{
  "openCode": "", //user CODE, must be uploaded
  "openId": "",  //user ID, must be uploaded
  "friendOpenIdList": ["111","222"] //User ID collection of friends, must be uploaded
  
}
```

**Response**:

```
{
	"code": 200, //error code, 200 is success，others are fail
	"data": {
            "111": true,
            "222": false
        },
	"message": ""  //wrong description
}
```



# Room interface -- only for game server

| Interface                              | Description                                                  |
| -------------------------------------- | ------------------------------------------------------------ |
| Create a room                          | The game server can call the MGS room creation interface after creating the room |
| Destroy the room                       | If the game room is destroyed, it needs to be synchronized to MGS for room destruction. |
| Synchronize room information           | Synchronize room information, synchronize the modified content each time |
| Remove room members                    | Use in scenarios where abnormal users need to be deleted when users are offline |
| Search room (optional)                 | Query the room number of the game party through the MGS room number, and the MGS SDK also provides an interface with the same capabilities |
| Query room information (for debugging) | Query room information for debugging                         |
| Query room records (for debugging)     | Can query all the records of the entire life cycle of the room, used to debug and compare whether the operations on the game side and mgs are consistent, used for debugging |

## Create room

**Interface description**:

Optional, the Game Server can call MGS's Create Room interface to synchronize the room information after creating the room. (`PS: the client still needs to call the onCreate method again for synchronization)`

**Interface address**:`/api/cp/mgsCp/room/create`

**Request mode**:`POST`

**Request data type**:`application/json`



**Request**:


```javascript
{
  "roomIdFromCp": "111dddd", //Room ID from the game party, must be uploaded
  "roomLimit": 4, //Room member capacity, must be uploaded
  "roomName": "今天天气真好", //Room name, must be uploaded
  "roomState":0, // Room status: 0 in preparation, 1 in game, 2 game over, must be uploaded
  "roomTags": ["大乱斗","5V5"] //Room tag, don’t pass if you don’t have it, optional to upload
  "canVoice": true, // Whether to use open microphone, it is enabled by default, optional to upload
  "canRoomChat": true // Whether to use the chat room, open by default, optional  to upload
```

**Response**:

```javascript
{
	"code": 200,//Error code, 200 is success, others are failures
	"data": {
		"roomIdFromCp": "111dddd", //Room ID from the game party
		"roomLimit": 0, //Room member capacity
		"roomName": "今天天气不错", //Room name
        "roomShowNum": "100123", //MGS room number
		"roomState": 0, //Room status: 0 in preparation, 1 in game, 2 game over
        "roomTags": ["大乱斗","5V5"], //Room tag
        "canVoice": true, // Whether to use open wheat
        "canRoomChat": true, // Whether to use chat room
        "memberList": [], //Room member list
    },
	"message": "" //wrong description
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
   "roomIdFromCp": "", //Room ID from the game party, must be passed
   "roomName": "", // Optional, room name
   "roomState": 0, //Optional, room state: 0 in preparation, 1 in game, 2 in game over
   "roomTags": ["Chaos","5V5"] //Optional, room tag, array
}
```

**Response**:

```javascript
{
   "code": 200, //error code, 200 is success, others are failures
   "data": true, //return true for success, false for failure
   "message": "" //error description
}
```



## Remove room members

**Interface description**:

**Used in scenarios where abnormal users need to be deleted when the user is offline**

When this interface is called, the MGS server will notify other members in the room that a member has left the room

**Interface address**:`/api/cp/mgsCp/room/removeMember`

**Request form**:`POST`

**Request data type**:`application/json`

**Request**:

```
{
   "roomIdFromCp": "", //Room ID from the game party, must be passed
   "openId": "2ffefdgdsfsdf" //Removed member, must be passed
}
```

**Response**:

```
{
   "code": 200, //error code, 200 is success, others are failures
   "data": true, //return true for success, false for failure
   "message": "" //error description
}
```



## Search room(optional)

**Interface description**:

Optional, query the room number of the game party through the MGS room number
At the same time, the MGS SDK also provides an interface with the same capabilities

**Interface Address**: `/api/cp/mgsCp/room/search`

**Request method**: `POST`

**Request data type**: `application/json`

**request**:

```
{
  "roomShowNum": "101021", //MGS room number, must upload
}
```

**Response**:

```
{
   "code": 200, //error code, 200 is success, others are failures
   "data": "13124124", //Game room number
   "message": "" //error description
}
```

## Query room information (optional, for debugging)

**Interface description**:

Query room information, optional, used for debugging

**Interface Address**: `/api/cp/mgsCp/room/queryInfo`

**Request method**: `POST`

**Request data type**: `application/json`

**request**:

```
{
  "roomIdFromCp": "" //Room ID from the game party, must upload
}
```

**Response**:

```
{
   "code": 200,//error code, 200 is success, others are failures
   "data": {
     "memberList": [], //Room member list
     "roomIdFromCp": "", //room ID from the game party
     "roomLimit": 0, //Room member capacity
     "roomName": "", //room name
     "roomShowNum": "100123", //MGS room number
     "roomState": 0, //room state: 0 in preparation, 1 in game, 2 in game over
     "roomTags": ["Chaos","5V5"], //room tags
     "canVoice": true, // Whether to use open microphone
     "canRoomChat": true // Whether to use the chat room
   },
   "message": "" //error description
}
```

## Query room records (optional, for debugging)

**Interface description**:

Can query all records of the entire life cycle of the room, used to debug and compare whether the operations on the game side and mgs are consistent, optional, used for debugging

**Interface Address**: `/api/cp/mgsCp/room/queryRecord`

**Request method**: `POST`

**Request data type**: `application/json`

**request**:

```
{
  "roomIdFromCp": "", //Room ID from the game party, must upload
}
```

**Response**:

```
{
  "code": 200,
          "message": "Success",
          "data": [
    {
      "content": "[create Room] source(MGS SDK),name(太空),showNum(102546),limit(10),state(0)",
      "time": "2021-03-26 15:29:29"
    },
    {
      "content": "[join Room] openId(0e4360d19fc4e4a12969a0c10bb3d5cd)",
      "time": "2021-03-26 15:29:29"
    },
    {
      "content": "[syncInfo] name(太空:102546),state(0),",
      "time": "2021-03-26 15:29:31"
    },
    {
      "content": "[join Room] openId(0f5c191071b6d64c667aabd052867095)",
      "time": "2021-03-26 15:29:43"
    },
    {
      "content": "[join Room] openId(6b230940da1583627527087867097502)",
      "time": "2021-03-26 15:29:44"
    },
    {
      "content": "[join Room] openId(8a0d848328be304ceea14e729fbf6bcb)",
      "time": "2021-03-26 15:31:13"
    },
    {
      "content": "[join Room] openId(bcc6aae27091cb498d95906d34cd62bf)",
      "time": "2021-03-26 15:31:14"
    },
    {
      "content": "[join Room] openId(7f7c3e0c9cab785c0eafc183d997ee8a)",
      "time": "2021-03-26 15:31:58"
    },
    {
      "content": "[syncInfo] name(太空:102546),state(1),",
      "time": "2021-03-26 15:32:19"
    },
    {
      "content": "[destroy] source(CPServer)",
      "time": "2021-03-26 15:33:26"
    }
  ]
}
```

# Team interface-dedicated to game server (optional)

| Interface                              | Description                                                     |
| -------------------------------------- | ------------------------------------------------------------ |
| Create a team                          | The game server can call the MGS team creation interface after creating a team |
| Destroy the team                       | If the game team is destroyed, it needs to be simultaneously destroyed by MGS. |
| Synchronize team information           | Synchronize team information, the content modified each time |
| Synchronize team members (must use)    | The game party needs to synchronize to MGS according to the information of the members in the team, and synchronize the data of all members every time |
| Remove team members                    | Use in scenarios where abnormal users need to be deleted, such as when users are offline |
| Query team information (for debugging) | Query team information for debugging                         |

## Create Team

**Interface description**:

The game server can call the team creation interface of MGS after team creation.

**Interface Address**: `/api/cp/mgsCp/team/create`

**Request method**: `POST`

**Request data type**: `application/json`

**request**:

```
{
   "roomIdFromCp": "111dddd", //Team ID from the game, must upload
   "roomLimit": 4, //Team member capacity, must pass
   "roomName": "The weather is really nice today", //Team name, must upload
   "roomState":0, // Team state: 0 in preparation, 1 in game, 2 in game over, must upload
   "roomTags": ["Chaos Fight","5V5"] //Team tag, don’t pass if there is none, select
   "canVoice": true, // Whether to use open microphone, it is enabled by default, optional upload
   "canRoomChat": true, // Whether to use the chat room, open by default, optional upload
   "parentIdFromCp": "123124", // Parent room ID (from the game party)
   "voiceScope": 2, //Voice capability range: 0 None, 1Room, 2Team
   "roomChatScope": 1, // Chat room capability range: 0 None, 1Room, 2Team
}
```

**Response**:

```
{
"code": 200,//error code, 200 is success, others are failures
"data": {
"roomIdFromCp": "111dddd", //room ID from the game party
"roomLimit": 0, //Room member capacity
"roomName": "The weather is nice today", //room name
         "roomShowNum": "100123", //MGS room number
"roomState": 0, //room state: 0 in preparation, 1 in game, 2 in game over
         "roomTags": ["Chaos","5V5"], //room tags
         "canVoice": true, // Whether to use open microphone
         "canRoomChat": true, // Whether to use the chat room
         "memberList": [], //Room member list
         "parentIdFromCp": "123124", // Parent room ID (from the game party)
         "voiceScope": 2, //Voice capability range: 0 None, 1Room, 2Team
         "roomChatScope": 1, // Chat room capability range: 0 None, 1Room, 2Team
     },
"message": "" //error description
}
```

## Destroy Team 

**Interface description**:

If the game team is destroyed, the team must be destroyed simultaneously to MGS.

**Interface Address**: `/api/cp/mgsCp/team/destroy`

**Request method**: `POST`

**Request data type**: `application/json`

**Request**:

```
{
  "roomIdFromCp": "" //The team ID from the game party, must upload
}
```

**Response**:

```
{
"code": 200, //error code, 200 is success, others are failures
"data": true, //return true for success, false for failure
"message": "" //error description
}
```

## Synchronize team information

**Interface description**:

Synchronize team information, synchronize the modified content each time
This interface is called only when there is information change, for example, the game state changes from **in preparation** to **in-game**

**Interface Address**: `/api/cp/mgsCp/team/syncInfo`

**Request method**: `POST`

**Request data type**: `application/json`

**request**:

```
{
   "roomIdFromCp": "", //Team ID from the game party, must upload
   "roomName": "", // optional, team name
   "roomState": 0, //Optional, team status: 0 in preparation, 1 in game, 2 game over
   "roomTags": ["Chaos","5V5"] //Optional, team tag, array
}
```

**Response**:

```
{
   "code": 200, //error code, 200 is success, others are failures
   "data": true, //return true for success, false for failure
   "message": "" //error description
}
```

## Remove team members

**Interface description**:

**Used in scenarios where abnormal users need to be deleted when the user is offline**
When this interface is called, the MGS server will notify other members of the team that some members have left the team

**Interface Address**: `/api/cp/mgsCp/team/removeMember`

**Request method**: `POST`

**Request data type**: `application/json`

**Request**:

```
{
   "roomIdFromCp": "", //Team ID from the game party, must upload
   "openId": "2ffefdgdsfsdf" //Removed member, must upload
}
```

**Response**:

```
{
   "code": 200, //error code, 200 is success, others are failures
   "data": true, //return true for success, false for failure
   "message": "" //error description
}
```

## Query team information (optional, for debugging)

**Interface description**:

Query team information, optional, for debugging

**Interface Address**: `/api/cp/mgsCp/team/queryInfo`

**Request method**: `POST`

**Request data type**: `application/json`

**Request**:

```
{
  "roomIdFromCp": "" //The team ID from the game party, must upload
}
```

**Response**:

```
{
   "code": 200,//error code, 200 is success, others are failures
   "data": {
     "memberList": [], //Team member list
     "roomIdFromCp": "", //The team ID from the game party
     "roomLimit": 0, //Team member capacity
     "roomName": "", //Team name
     "roomShowNum": "100123", //MGS team number
     "roomState": 0, //Team state: 0 in preparation, 1 in game, 2 in game over
     "roomTags": ["Chaos","5V5"], //Team tag
     "canVoice": true, // Whether to use open microphone
     "canRoomChat": true,// Whether to use the chat room
     "parentIdFromCp": "123124", // Parent room ID (from the game party)
     "voiceScope": 2, //Voice capability range: 0 None, 1Room, 2Team
     "roomChatScope": 1, // Chat room capability range: 0 None, 1Room, 2Team
   },
   "message": "" //error description
}
```

## Query team records (optional, for debugging)

**Interface description**:

Can query all records of the team's entire life cycle, used to debug and compare whether the operations on the game side and mgs are consistent, optional, used for debugging

**Interface Address**: `/api/cp/mgsCp/team/queryRecord`

**Request method**: `POST`

**Request data type**: `application/json`

**request**:

```
{
  "roomIdFromCp": "", //The team ID from the game party, must be passed
}
```

**响应**:

```
{
  "code": 200,
          "message": "Success",
          "data": [
    {
      "content": "[create Team] source(MGS SDK),name(太空),showNum(102546),limit(10),state(0)",
      "time": "2021-03-26 15:29:29"
    },
    {
      "content": "[join Team] openId(0e4360d19fc4e4a12969a0c10bb3d5cd)",
      "time": "2021-03-26 15:29:29"
    },
    {
      "content": "[syncInfo] name(太空:102546),state(0),",
      "time": "2021-03-26 15:29:31"
    },
    {
      "content": "[join Team] openId(0f5c191071b6d64c667aabd052867095)",
      "time": "2021-03-26 15:29:43"
    },
    {
      "content": "[join Team] openId(6b230940da1583627527087867097502)",
      "time": "2021-03-26 15:29:44"
    },
    {
      "content": "[join Team] openId(8a0d848328be304ceea14e729fbf6bcb)",
      "time": "2021-03-26 15:31:13"
    },
    {
      "content": "[join Team] openId(bcc6aae27091cb498d95906d34cd62bf)",
      "time": "2021-03-26 15:31:14"
    },
    {
      "content": "[join Team] openId(7f7c3e0c9cab785c0eafc183d997ee8a)",
      "time": "2021-03-26 15:31:58"
    },
    {
      "content": "[syncInfo] name(太空:102546),state(1),",
      "time": "2021-03-26 15:32:19"
    },
    {
      "content": "[destroy] source(CPServer)",
      "time": "2021-03-26 15:33:26"
    }
  ]
}
```



## Synchronize room members(suggest to apply)

**Interface description**:

Game side needs to synchronize the information of members in the room to MGS, and synchronize the data of all members each time

**Interface address**:`/api/cp/mgsCp/room/syncMember`

**Request mode**:`POST`

**Request data type**:`application/json`

**Request**:

```javascript
{
  "roomIdFromCp": "", //The room ID from CP, must upload
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

