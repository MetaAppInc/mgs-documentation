# `MGS Android SDK` API Guide

## Access Method

`android studio`

1. Download the SDK and add the provided package `mgs.aar` to the libs directory of the project.
2. Add `aar` dependency to `build.gradle` of your project.

## Initialization

The SDK needs to be initialized before it can call any other interface.

API interface:

```java
/**
 * SDK Initialization interface
 * @param context context
 * @param apiKey developer platform applied apiKey
 * @param listener  Initializes the callback
 */
public void initSdk(Context context, String apiKey, MgsInitListener listener)
```

Code sample:

```java
MgsApi.getInstance().initSdk(context, API_KEY, new MgsInitListener() {
            @Override
            public void onSuccess() {
                resultOutputView.setText("Initialize success");
            }

            @Override
            public void onFail(int code, String message) {
                resultOutputView.setText("Initialize fail");
            }
        });
```

## SDK Dynamic Function Interface

SDK provides an interface for dynamic function calls. Such as log in, create rooms, leave rooms, etc.

Unified callback interface: `MgsFeatureListener`

```java
public interface MgsFeatureListener {
    /**
     * success callback
     * @param requestCode Request code
     * @param resultJson  json string results(different interfaces return different values)
     */
    void onSuccess(int requestCode, String resultJson);

    /**
     * fail callback
     * @param requestCode Request code
     * @param code  error code
     * @param message error description
     */
    void onFail(int requestCode, int code, String message);
}

```

Code error:

- Public:    
  `10100`: initialization failed  
  `10200`: Service connection failed  
  `400`: Parameter error  
  `500`: System abnormal, please try again later or contact customer service personnel  
  `401`: Authentication error

- Room related:  
  `80001`: Room does not exist  
  `80002`: Room destroyed   
  `80003`: Room is full  
  `80004`: Room operation is not allowed

API Interface:

```java
    /**
     * Dynamic functional interface
     * @param feature interface name ("login","queryPlayerAction","createRoom",etc.)
     * @param requestCode Request code, the game can distinguish the request based on the business characteristics, the default can pass 0
     * @param jsonParams  JSON format parameter
     * @param listener   callback listens
     */
    void invokeFeature(String feature, int requestCode, String jsonParams, MgsFeatureListener listener);
```

feature interface list description:

`login` login  
`queryPlayerAction` query player's actions  
`createRoom` create room  
`joinRoom` join room  
`leaveRoom` leave room  
`showUserProfile` show player's profile  
`isFriendShip` detect if the player is a friend  
`showFloatingLayer`  show hover(floating) layer (chat:0 / friends:1)

**Login - Example**

Game side need to call the `login` interface before they can use any other interface.

`call example`

```java
//Request code, the game can distinguish the request based on the business characteristics, the default can pass 0
int requestCode = 1000;

//call the login interface
MgsApi.getInstance().invokeFeature("login", requestCode, null, new MgsFeatureListener() {
    @Override
    public void onSuccess(int requestCode, String resultJson) {

        //resultJson = {"openCode":"e1c575ca8a7732711e7a8c2f8b9f07dd","openId":"52767f66607dcea8558de6bccd7a270d"}
       
       if (requestCode == 1000) {
           //json format conversion, which is done using Gson, may be done using different conversion tools depending on the game's needs.
           Map<String, String> result = new Gson().fromJson(resultJson,
                new TypeToken<Map<String, String>>() {}.getType());

          //player openId
          String openId = result.get("openId");
          //player openCode
          String openCode = result.get("openCode");

          //Notify the Game Server of login results
          requestLoginResultToServer(openId, code);
       } 
    }

    @Override
    public void onFail(int requestCode, int code, String message) {
        ToastUtils.showToast(getContext(), "Mgs login fail callback "+message);
    }
}); 
```

`Request parameters`

None

`Return value`

```java
{
  "openId":"52767f666072cea8553de6bccd7a270d", //player openId, used for unique identification
  "openCode":"e1c575ca8a7732711e7a8c2f8b9f07dd" //player openCode, User Validation
}
       
```

**Queries player's actions when entering a room - Example**

Game side can call the interface when the player enters the game at the appropriate time, query the actions when the player enters the game, game side can carry out the corresponding business logic processing according to the operation.

`call example`

```java
//Request code, the game can distinguish the request based on the business characteristics, the default can pass 0
int requestCode = 0;
//Call to query how the player entered the room
MgsApi.getInstance().invokeFeature("queryPlayerAction", requestCode, null, new MgsFeatureListener() {
    @Override
    public void onSuccess(int requestCode, String resultJson) {
       // resultJson = {"action":0, "roomIdFromCp": "123456"} 
       // action The player's action when entering a room, which is 0: join room 1: create room 2: quick join room
      // roomIdFromCp The game syncs to the MGS Roomid
    }

    @Override
    public void onFail(int requestCode, int code, String message) {
        //fail callback result
    }
});
```

`Request parameters`

None

`Return value`

```java
{ 
"action":0,  //The player's action when entering a room, which is 0: join room 1: create room 2: quick join room
"roomIdFromCp": "123456" //Game side room number
} 
```

**Create a room - Example**

After creating the room, the game side can synchronize the data by calling `createRoom`, or synchronize the data through the MGS server.

`call example`

```java
//Request code
String params = "{\"roomIdFromCp\":\"1234\",\"roomName\":\"测试房\",\"roomLimit\":2}";

//Request code, the game can distinguish the request based on the business characteristics, the default can pass 0
int requestCode = 0;
//create room
MgsApi.getInstance().invokeFeature("createRoom", requestCode, params, new MgsFeatureListener() {
            @Override
            public void onSuccess(int requestCode, String resultJson) {
              //create room success
              //resultJson = {"roomIdFromCp":"1234","roomLimit":2,"roomName":"房间名称", "roomState":0, "roomShowNum": "100038"} 

            }

            @Override
            public void onFail(int requestCode, int code, String message) {
                //create faill callback
            }
        });
```

`Request parameters`

```java
{
  "roomIdFromCp":"1234", //Game side room number
  "roomName":"测试房", //Game side room name
  "roomLimit":2
}
```

`Return value`

```java
{
  "roomIdFromCp":"1234", //Game side room number
  "roomLimit":2, //Game side room capacity
  "roomName":"房间名称", //room name
  "roomState":0,  //Room status, value (0: joinable 1: playing 2: destroyed)
  "roomShowNum": "100038"// Room display number
} 
```

**Join a room - Example**

After players join a room, game side need to synchronize the data by calling `joinRoom`

`call example`

```java
 
 //Request code
String params = "{\"roomIdFromCp\":\"1234\"}";
//Request code, the game can distinguish the request based on the business characteristics, the default can pass 0
int requestCode = 0;
//join room
MgsApi.getInstance().invokeFeature("joinRoom", requestCode, params, new MgsFeatureListener() {
            @Override
            public void onSuccess(int requestCode, String resultJson) {
                //join room success callback
                 // resultJson = {"roomIdFromCp":"1234","roomLimit":2,"roomName":"房间名称", "roomState":0, "roomShowNum": "100038"} 
            }

            @Override
            public void onFail(int requestCode, int code, String message) {
                //fail callback
            }
        });
```

`Request parameters`

```java
{
  "roomIdFromCp":"1234"  //Game side room number
}
```

`Return value`

```java
{
  "roomIdFromCp":"1234", //Game side room number
  "roomLimit":2, //Game side room capacity
  "roomName":"房间名称", //room name
  "roomState":0,  //Room status, value (0: joinable 1: playing 2: destroyed)
  "roomShowNum": "100038"// Room display number
} 
```

**Leave room-example**

Game side needs to call `leaveRoom` for data synchronization when the player joins and leaves the room.

`call example`

```java
String params = "{\"roomIdFromCp\":\"1234\"}";
//Request code, the game can distinguish the request based on the business characteristics, the default can pass 0
int requestCode = 0;
//leave room
MgsApi.getInstance().invokeFeature("leaveRoom", requestCode, params, new MgsFeatureListener() {
            @Override
            public void onSuccess(int requestCode, String resultJson) {
                //leave room success callback
                //resultJson = {"data": true}
            }

            @Override
            public void onFail(int requestCode, int code, String message) {
                //fail callback
            }
        });
```

``Request parameters``

```java
{
  "roomIdFromCp":"1234"  //Game side room number
}
```

`Return value`

```java
{
  "data": true, //leave room status, true success  fase:fail 
}
```

**View the Player Profile - Example**

If you need to view the profile information of a 233 players, you can call `showUserProfile` to view it, and the SDK will pop up the profile card popup window.

`call example`

```java
//Request code
String params = "{\"openId\":\"1234\"}";
//Request code, the game can distinguish the request based on the business characteristics, the default can pass 0
int requestCode = 0;
//Displays the player profile card
MgsApi.getInstance().invokeFeature("showUserProfile", requestCode, params, null);
```

`Request parameters`

```java
{
  "openId":"233233233dfadfaet"  //player openId
}
```

`Return value`

None

**Checking if a player is a friend - Example**

If you want to check whether the player is a friend, you can call the `isFriendShip` interface to check it.

`call example`

```java
//Request code
String params = "{\"friendOpenId\":\"1234\"}";

//Request code, the game can distinguish the request based on the business characteristics, the default can pass 0
int requestCode = 0;
//Call whether the player is a friend or not
MgsApi.getInstance().invokeFeature("isFriendShip", requestCode, params, new MgsFeatureListener() {
            @Override
            public void onSuccess(int requestCode, String resultJson) {
                //success callback
            }

            @Override
            public void onFail(int requestCode, int code, String message) {
                //fail callback
            }
        });
```

`Request parameters`

```java
{
  "friendOpenId":"12322323234"  //The openId of the player to check
}
```

`Return value`

```java
{
  "data": true, //friend or not，true: friend  fase: not friend
}
```

**Display Floating Windows - example**

Game side can call `showFloatingLayer` to expand the contents of the hover(floating) layer, and chat/friends functions can be activated

`call example`

```java
//Request code
String params = "{\"tab\": 1}";

//Request code, the game can distinguish the request based on the business characteristics, the default can pass 0
int requestCode = 0;
//Call whether the player is a friend or not
MgsApi.getInstance().invokeFeature("showFloatingLayer", requestCode, params, null);
```

`Request parameters`

```java
{
  "tab": 1  //hover layer function position value (0: chat 1: friends)
}
```

`Return value`

None

**Display game exit confirmation box - Example**

Game side can call `showExitGameDialog` to display the exit confirmation box.

`call example`

```java
  
//Call whether the player is a friend or not
MgsApi.getInstance().invokeFeature("showExitGameDialog", 0, null, null);
```

`Request parameters`

None

`Return value`

None


## The log report

Game side calls the `reportLogInfo` interface to report the event tracking data needed for operation.

API interface:

```java
/**
 * event tracking log reporting interface
 * @param event event tracking label
 * @param eventDesc event tracking description
 * @param data event tracking parameter data
 */
void reportLogInfo(String event, String eventDesc, String data);
```

`call example`

```java
/**
 * game event tracking business log 
 */
private void reportGameLogToMgs(int roomType, int roomLimit) {

    try {
        //event tracking parameter
        JSONObject eventObject = new JSONObject();
        eventObject.put("room_type", roomType);
        eventObject.put("room_limit", roomLimit); 
        //report log
         MgsApi.getInstance().reportLogInfo("event_yc_enter_room", "进入房间事件",  eventObject.toString());
    } catch (JSONException e) {
        e.printStackTrace();
    } 
}
 
```

`Request parameters`

Transfer parameter according to the event tracking plan provided by the operation side.

`Return value`

None

## Globally listen for SDK notification events

SDK provides global listeners function, SDK would internally notify the game globally of what to do.

API interface:

```java
/**
 * Register a global broadcast listener for the SDK to actively notify the game
 * @param event event name
 * @param listener listen callback
 */
 public void registerMgsEventListener(String event, MgsEventListener listener)；
```

`Exit game event notification`:

```java
MgsApi.getInstance().registerMgsEventListener("exitGameEvent", new MgsEventListener() {
    @Override
    public void onMgsEventHandle(String jsonData) {
        //Exit the game event notification, game side can do some game cleaning work, such as cleaning room, etc
    }
});
```

`Change room name`:

```java
MgsApi.getInstance().registerMgsEventListener("changeRoomNameEvent", new MgsEventListener() {
    @Override
    public void onMgsEventHandle(String jsonData) {
        //resultJson = {"roomIdFromCp":"1121","roomName":"modified name"}
        //Synchronize to the game server
    }
});
```

## SDK Destroy

The SDK provides a method to destroy the SDK after the game exits to release the corresponding memory. The next time you call another interface, you need to initialize it again.

API interface:

```java
/**
 * SDK destroy event
 */
void destroySdk();
```

code example:

```java
 MgsApi.getInstance().destroySdk();
```

**More examples**

For more examples, see the `MgsSdkBridgeHelper` class in the DEMO

`MgsSdkBridgeHelper` is a quick access package provided by SDK, which can be directly used by the game or used for secondary packaging based on `MgsSdkBridgeHelper` class according to business characteristics.