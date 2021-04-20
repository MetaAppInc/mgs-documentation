<!-- TOC -->

- [`MGS安卓SDK` API文档][1]
	- [接入方式][2]
	- [初始化][3]
	- [SDK动态功能接口][4]
		- [登录][5]
		- [查询接口是否可用][6]
		- [查询玩家进入房间时的操作方式][7]
		- [创建房间][8]
		- [加入房间][9]
		- [离开房间][10]
		- [切换房间][11]
		- [加入队伍][12]
		- [离开队伍][13]
		- [查看玩家资料卡片][14]
		- [检测玩家是否是好友关系][15]
		- [添加好友-示例][16]
		- [显示悬浮窗][17]
		- [显示游戏退出确认框][18]
	- [获取当前233乐园环境][19]
	- [日志上报][20]
	- [全局监听SDK通知事件][21]
		- [退出游戏事件通知][22]
		- [MGS房间销毁通知][23]
	- [SDK销毁][24]

<!-- /TOC -->

# `MGS安卓SDK` API文档

## 接入方式

`android studio`

1. 下载SDK并将提供的`mgs.aar`包添加到工程的libs目录下
2. 在项目的`build.gradle`中添加`aar`依赖即可

## 初始化

SDK需要先进行初始化操作，方可调用其他接口。

API接口：
```java
/**
 * SDK初始化接口
 * @param context context
 * @param apiKey 开发者平台申请的apiKey
 * @param listener  初始化回调
 */
public void initSdk(Context context, String apiKey, MgsInitListener listener)
```

代码示例:

```java
 MgsApi.getInstance().initSdk(context, API_KEY, new MgsInitListener() {
            @Override
            public void onSuccess() {
                resultOutputView.setText("初始化成功");
            }

            @Override
            public void onFail(int code, String message) {
                resultOutputView.setText("初始化失败");
            }
        });
```

## SDK动态功能接口

SDK提供动态功能调用的接口,例如登录、创建房间、离开房间等功能。

统一回调接口:`MgsFeatureListener`

```java
public interface MgsFeatureListener {
    /**
     * 成功回调
     * @param requestCode 请求码
     * @param resultJson  json字符串结果(不同的接口返回值不一样)
     */
    void onSuccess(int requestCode, String resultJson);

    /**
     * 失败回调
     * @param requestCode 请求码
     * @param code  错误码
     * @param message 错误描述信息
     */
    void onFail(int requestCode, int code, String message);
}

```

code错误码:

- 公用:  
	  `10100`: 初始化失败  
	  `10200`: 服务连接失败  
	  `400`: 参数错误  
	  `500`: 系统异常，请稍后再试或联系客服人员  
	  `401`: 身份验证错误

- 房间相关:  
	  `80001`: 房间不存在  
	  `80002`: 房间已销毁  
	  `80003`: 房间已满  
	  `80004`: 房间不允许操作


API接口：

```java

/**
 * 动态功能接口
 * @param feature 接口名称 ("login","queryPlayerAction",etc.)
 * @param requestCode 请求码,游戏可根据业务特征来就进行区分是哪次请求,默认可传0
 * @param jsonParams  json格式参数
 * @param listener   回调监听
 */
void invokeFeature(String feature, int requestCode, String jsonParams, MgsFeatureListener listener);

```

feature接口列表描述:

`login` 登录  
`queryPlayerAction` 查询玩家操作方式  
`createAndJoinRoom` 创建并加入房间  
`joinRoom` 加入房间  
`leaveRoom` 离开房间  
`joinTeam` 加入队伍  
`leaveTeam` 离开队伍  
`addFriend` 添加好友  
`showUserProfile` 查看玩家资料信息  
`isFriendShip` 检测玩家是否好友关系  
`showFloatingLayer` 显示悬浮层(聊天:0 / 好友:1)  
`getCpRoomIdByRoomShowNum` 根据233房间号查找游戏方的房间号  


### 登录

游戏方在使用其他接口前，需要先调用`login`接口。

`调用示例`

```java

//请求码,游戏可根据业务特征来就进行区分是哪次请求,默认可传0
int requestCode = 1000;

//调用登录接口
MgsApi.getInstance().invokeFeature("login", requestCode, null, new MgsFeatureListener() {
    @Override
    public void onSuccess(int requestCode, String resultJson) {

        //resultJson = {"openId":"玩家openId","openCode":"玩家openCode","avatar":"头像地址","nickname":"昵称"}
       
       if (requestCode == 1000) {
           //json格式转换,这里使用Gson进行转换，游戏可能就自身需求使用不同的转换工具进行转换
           Map<String, String> result = new Gson().fromJson(resultJson,
                new TypeToken<Map<String, String>>() {}.getType());

          //玩家openId
          String openId = result.get("openId");
          //玩家openCode
          String openCode = result.get("openCode");
          //玩家头像
          String avatar = result.get("avatar");
          //玩家昵称
          String nickname = result.get("nickname");

          //上报登录结果给游戏服务端
          requestLoginResultToServer(openId, openCode, nickname, avatar);
       } 
    }

    @Override
    public void onFail(int requestCode, int code, String message) {
        ToastUtils.showToast(getContext(), "Mgs login fail callback "+message);
    }
}); 
```

`请求参数`

无

`返回值`

```java
{
  "openId":"52767f666072cea8553de6bccd7a270d", //玩家openId, 用于唯一标识   
  "openCode":"e1c575ca8a7732711e7a8c2f8b9f07dd", //玩家openCode,用户有效性验证   
  "avatar":"玩家头像", //233玩家头像   
  "nickname":"玩家昵称" //233玩家昵称  
}  
       
```

### 查询接口是否可用

SDK提供了`isSupportedFeature`接口，用于查询接口是否可用。


`调用示例`

```java
boolean isSupported = MgsApi.getInstance().isSupportedFeature("login");

if(isSupported) {
  //接口可用
} else {
  //接口不可用
}

```

### 查询玩家进入房间时的操作方式

游戏方可在玩家进入到游戏适当的时候调用该接口，查询玩家进入游戏时的操作方式，游戏方可根据操作进行相应的业务逻辑处理。

`调用示例`

```java

//请求码,游戏可根据业务特征来就进行区分是哪次请求,默认可传0
int requestCode = 0;
//调用查询玩家进入房间的操作方式
MgsApi.getInstance().invokeFeature("queryPlayerAction", requestCode, null, new MgsFeatureListener() {
    @Override
    public void onSuccess(int requestCode, String resultJson) {
       // resultJson = {"action":0,"mgsCpRead":false, "roomIdFromCp":"716","inviteOpenId":"123"}
       // action 玩家在233大厅选择进入房间时的操作方式,取值 -1:无任何操作 0: 加入房间 1: 创建房间  2: 快速加入房间
      // roomIdFromCp 游戏方的roomId,加入房间会有该值，其他action不会有
      // mgsCpRead 首次查询返回false，若退到后台再返回重新查询玩家操作，会返回true
      //inviteOpenId 邀请人的openId
    }

    @Override
    public void onFail(int requestCode, int code, String message) {
        //失败回调结果
    }
});
```

`请求参数`

无

`返回值`

```java
{ 
"action":0,  //玩家在233大厅选择进入房间时的操作方式,取值 -1:无任何操作 0: 加入房间 1: 创建房间  2: 快速加入房间
"roomIdFromCp": "123456", //游戏方房间号
"mgsCpRead":false, //首次会返回false，若退到后台再返回重新查询玩家操作，会返回true，游戏根据业务进行处理。
"inviteOpenId":"123" //邀请人的openId
} 
```

### 创建房间  

游戏方创建好房间后可通过调用`createAndJoinRoom`进行数据同步，也可通过MGS服务端进行数据同步。

`调用示例`

```java

//请求参数
String params = "{\"roomIdFromCp\":\"1234\",\"roomName\":\"测试房\",\"roomLimit\":2}";

//请求码,游戏可根据业务特征来就进行区分是哪次请求,默认可传0
int requestCode = 0;
//创建房间
MgsApi.getInstance().invokeFeature("createAndJoinRoom", requestCode, params, new MgsFeatureListener() {
            @Override
            public void onSuccess(int requestCode, String resultJson) {
              //创建房间成功
              //resultJson = {"parentRoomIdFromCp":null,"roomIdFromCp":"游戏方房间号","roomLimit":8,"roomName":"房间名","roomShowNum":"103216","roomState":0,"roomTags":null}
            }

            @Override
            public void onFail(int requestCode, int code, String message) {
                //创建失败回调
            }
        });
```

`请求参数`

```java
{
  "roomIdFromCp":"1234", //游戏方房间号
  "roomName":"测试房", //游戏方房间名称
  "roomLimit":2
}
```


`返回值`

```java

{
  "roomIdFromCp":"1234", //游戏方房间号
  "roomLimit":2, //游戏方房间容量
  "roomName":"房间名称", //房间名称
  "roomState":0,  //房间状态，取值 - 0: 可加入 1: 正在玩 (不可加入) 2: 游戏结束
  "roomShowNum": "100038",// MGS房间号
  "parentRoomIdFromCp":null, //组队模式会返回该ID
  "roomTags":null //房间标签，返回的是数组["标签1","标签2"]
} 
```

### 加入房间  

游戏方在玩家加入某个房间后，需要通过调用`joinRoom`进行数据同步。


`调用示例`

```java
 
 //请求参数
String params = "{\"roomIdFromCp\":\"1234\"}";
//请求码,游戏可根据业务特征来就进行区分是哪次请求,默认可传0
int requestCode = 0;
//加入房间
MgsApi.getInstance().invokeFeature("joinRoom", requestCode, params, new MgsFeatureListener() {
            @Override
            public void onSuccess(int requestCode, String resultJson) {
                //加入房间成功回调
                 // resultJson =  {"parentRoomIdFromCp":null,"roomIdFromCp":"游戏方房间号","roomLimit":8,"roomName":"房间名","roomShowNum":"103216","roomState":0,"roomTags":null}
            }

            @Override
            public void onFail(int requestCode, int code, String message) {
                //失败回调
            }
        });
```

`请求参数`

```java
{
  "roomIdFromCp":"1234"  //游戏方房间号
}
```


`返回值`

```java
{
  "roomIdFromCp":"1234", //游戏方房间号
  "roomLimit":2, //游戏方房间容量
  "roomName":"房间名称", //房间名称
  "roomState":0,  //房间状态，取值 - 0: 可加入 1: 正在玩 (不可加入) 2: 游戏结束
  "roomShowNum": "100038",// MGS房间号 
  "parentRoomIdFromCp":null, //组队模式会返回该ID
  "roomTags":null //房间标签，返回的是数组["标签1","标签2"]
} 
```

### 离开房间

游戏方在玩家离开房间前，需要调用`leaveRoom`进行数据同步。

`调用示例`

```java
String params = "{\"roomIdFromCp\":\"1234\"}";
//请求码,游戏可根据业务特征来就进行区分是哪次请求,默认可传0
int requestCode = 0;
//离开房间
MgsApi.getInstance().invokeFeature("leaveRoom", requestCode, params, new MgsFeatureListener() {
            @Override
            public void onSuccess(int requestCode, String resultJson) {
                //离开房间成功回调
                //resultJson = "true"
            }

            @Override
            public void onFail(int requestCode, int code, String message) {
                //失败回调
            }
        });
```

`请求参数`

```java
{
  "roomIdFromCp":"1234"  //游戏方房间号
}
```


`返回值`

返回boolean类型字符串，需要进行转换 , `true`为离开成功，`false`为离开失败
  
### 切换房间 v2.1.0新增  

游戏方在玩家切换某个房间后，需要通过调用`switchRoom`进行数据同步。


`调用示例`

```java
 
 //请求参数
String params = "{\"originRoomIdFromCp\":\"1234\",\"targetRoomIdFromCp\":\"3312\"}";
//请求码,游戏可根据业务特征来就进行区分是哪次请求,默认可传0
int requestCode = 0;
//切换房间
MgsApi.getInstance().invokeFeature("switchRoom", requestCode, params, new MgsFeatureListener() {
            @Override
            public void onSuccess(int requestCode, String resultJson) {
                //切换房间成功回调
                 // resultJson =  {"parentRoomIdFromCp":null,"roomIdFromCp":"游戏方房间号","roomLimit":8,"roomName":"房间名","roomShowNum":"103216","roomState":0,"roomTags":null}
            }

            @Override
            public void onFail(int requestCode, int code, String message) {
                //失败回调
            }
        });
```

`请求参数`

```java
{
  "originRoomIdFromCp":"1234",  //当前游戏方房间号
  "targetRoomIdFromCp":"3312"   //要切换的游戏方房间号
}
```


`返回值`

```java
{
  "roomIdFromCp":"1234", //游戏方房间号
  "roomLimit":2, //游戏方房间容量
  "roomName":"房间名称", //房间名称
  "roomState":0,  //房间状态，取值 - 0: 可加入 1: 正在玩 (不可加入) 2: 游戏结束
  "roomShowNum": "100038",// MGS房间号 
  "parentRoomIdFromCp":null, //组队模式会返回该ID
  "roomTags":null //房间标签，返回的是数组["标签1","标签2"]
} 
```



### 加入队伍  

游戏方在玩家加入某个队伍后，需要通过调用`joinTeam`进行数据同步。


`调用示例`

```java
 
 //请求参数
String params = "{\"roomIdFromCp\":\"1234\", \"backRoomIdFromCp\":\"1233\"}";
//请求码,游戏可根据业务特征来就进行区分是哪次请求,默认可传0
int requestCode = 0;
//加入team
MgsApi.getInstance().invokeFeature("joinTeam", requestCode, params, new MgsFeatureListener() {
            @Override
            public void onSuccess(int requestCode, String resultJson) {
                //加入team成功回调
                 // resultJson = {"parentRoomIdFromCp":"720","roomIdFromCp":"721","roomLimit":2,"roomName":"妄赎vo0","roomShowNum":"103218","roomState":1,"roomTags":null}
            }

            @Override
            public void onFail(int requestCode, int code, String message) {
                //失败回调
            }
        });
```

`请求参数`

```java
{
  "roomIdFromCp":"1234"  //游戏方队伍ID
  "backRoomIdFromCp":"1233" //游戏方的RoomId（离开team要返回到哪个Room，如果为null，则默认返回父ROOM的ID）
}
```


`返回值`

```java
{
  "roomIdFromCp":"1234", //游戏方房间号
  "roomLimit":2, //游戏方房间容量
  "roomName":"房间名称", //房间名称
  "roomState":0,  //房间状态，取值 - 0: 可加入 1: 正在玩 (不可加入) 2: 游戏结束
  "roomShowNum": "100038",// MGS房间号 
  "parentRoomIdFromCp":"1233", //组队模式会返回该ID
  "roomTags":null //房间标签，返回的是数组["标签1","标签2"]
} 
```


### 离开队伍

游戏方在玩家离开队伍，需要调用`leaveTeam`进行数据同步。

`调用示例`

```java
String params = "{\"roomIdFromCp\":\"1234\"}";
//请求码,游戏可根据业务特征来就进行区分是哪次请求,默认可传0
int requestCode = 0;
//离开队伍
MgsApi.getInstance().invokeFeature("leaveTeam", requestCode, params, new MgsFeatureListener() {
            @Override
            public void onSuccess(int requestCode, String resultJson) {
                //离开房间成功回调
                //resultJson = "true"
            }

            @Override
            public void onFail(int requestCode, int code, String message) {
                //失败回调
            }
        });
```

`请求参数`

```java
{
  "roomIdFromCp":"1234"  //游戏方房间号
}
```


`返回值`

返回boolean类型字符串，需要进行转换 , `true`为离开成功，`false`为离开失败


### 查看玩家资料卡片

若需要查看233玩家的资料信息,可通过调用`showUserProfile`进行查看,SDK会弹出资料卡片弹窗。

`调用示例`

```java
//请求参数
String params = "{\"openId\":\"1234\"}";
//请求码,游戏可根据业务特征来就进行区分是哪次请求,默认可传0
int requestCode = 0;
//显示玩家资料卡
MgsApi.getInstance().invokeFeature("showUserProfile", requestCode, params, null);
```

`请求参数`

```java
{
  "openId":"233233233dfadfaet"  //玩家openId
}
```


`返回值`

无

### 检测玩家是否是好友关系

若需要检测玩家是否好友关系，可通过调用`isFriendShip`接口进行查看。

`调用示例`

```java

//请求参数
String params = "{\"friendOpenId\":\"1234\"}";

//请求码,游戏可根据业务特征来就进行区分是哪次请求,默认可传0
int requestCode = 0;
//调用玩家是否是好友关系
MgsApi.getInstance().invokeFeature("isFriendShip", requestCode, params, new MgsFeatureListener() {
            @Override
            public void onSuccess(int requestCode, String resultJson) {
                //成功回调 resultJson = "true"
            }

            @Override
            public void onFail(int requestCode, int code, String message) {
                //失败回调
            }
        });
```

`请求参数`

```java
{
  "friendOpenId":"12322323234"  //要查看的玩家openId
}
```


`返回值`  

返回boolean类型字符串，需要进行转换 , `true`为是好友，`false`为不是好友


### 添加好友-示例

若需要添加233好友，可通过调用`addFriend`接口进行添加好友。

`调用示例`

```java

//请求参数
String params = "{\"friendOpenId\":\"1234\"}";

//请求码,游戏可根据业务特征来就进行区分是哪次请求,默认可传0
int requestCode = 0;
//调用玩家是否是好友关系
MgsApi.getInstance().invokeFeature("addFriend", requestCode, params, new MgsFeatureListener() {
            @Override
            public void onSuccess(int requestCode, String resultJson) {
                //成功回调 resultJson = "true"
            }

            @Override
            public void onFail(int requestCode, int code, String message) {
                //失败回调
            }
        });
```

`请求参数`

```java
{
  "friendOpenId":"12322323234"  //要添加的玩家openId
}
```


`返回值`  

返回boolean类型字符串，需要进行转换 , `true`为成功，`false`为失败



### 显示悬浮窗

游戏方可调用`showFloatingLayer`来展开悬浮层的内容，可展开聊天/好友功能。

`调用示例`

```java

//请求参数
String params = "{\"tab\": 1}";

//请求码,游戏可根据业务特征来就进行区分是哪次请求,默认可传0
int requestCode = 0;
//调用玩家是否是好友关系
MgsApi.getInstance().invokeFeature("showFloatingLayer", requestCode, params, null);
```

`请求参数`

```java
{
  "tab": 1  //悬浮层功能位置 取值(0: 聊天 1: 好友)
}
```


`返回值`

无
  

### 显示游戏退出确认框

游戏方可调用`showExitGameDialog`来显示退出游戏确认框。

`调用示例`

```java
  
//调用玩家是否是好友关系
MgsApi.getInstance().invokeFeature("showExitGameDialog", 0, null, null);
```

`请求参数`

无

`返回值`

无

### 加入语音频道 v2.1.0新增

游戏方可调用`joinAudio`加入语音频道，游戏用户可使用语音服务（可说话、可听到其他游戏用户声音）。

`调用示例`

```java
  
//调用玩家是否是好友关系
MgsApi.getInstance().invokeFeature("joinAudio", 0, null, null);
```

`请求参数`

无

`返回值`

无

### 离开语音频道 v2.1.0新增

游戏方可调用`leaveAudio`离开语音频道，游戏用户不可使用语音服务（不可说话、不可听到其他游戏用户声音）。

`调用示例`

```java
  
//调用玩家是否是好友关系
MgsApi.getInstance().invokeFeature("leaveAudio", 0, null, null);
```

`请求参数`

无

`返回值`

无

### 语音静音 v2.1.0新增

游戏方可调用`muteAudio`语音静音（不可说话，可听到其他游戏用户声音）。

`调用示例`

```java
  
//调用玩家是否是好友关系
MgsApi.getInstance().invokeFeature("muteAudio", 0, null, null);
```

`请求参数`

无

`返回值`

无

### 语音开麦 v2.1.0新增

游戏方可调用`unmuteAudio`语音开麦（可说话，可听到其他游戏用户声音）。

`调用示例`

```java
  
//调用玩家是否是好友关系
MgsApi.getInstance().invokeFeature("unmuteAudio", 0, null, null);
```

`请求参数`

无

`返回值`

无

## 获取当前233乐园环境

游戏方在调试过程中可能需要检测233乐园版本环境，可通过`getCurrentEnvironment`进行检测。
可在初始化之前进行调用。


API接口:

```java

/**
 * 获取当前233环境
 * @param context
 * @return 返回整型, 取值范围(0:测试环境 1:预发环境  2:线上环境)
 */
int getCurrentEnvironment(Context context);

```

`调用示例`

```java

  
int envCode = MgsApi.getInstance().getCurrentEnvironment(context);
 //返回值 取值  0:测试环境 1:预发环境  2:线上环境
 
```

## 日志上报

游戏方调用`reportLogInfo`接口上报运营所需的埋点数据。

API接口:

```java
/**
 * 埋点日志上报接口
 * @param event 埋点事件标识
 * @param eventDesc 埋点事件描述
 * @param data 埋点事件参数数据
 */
void reportLogInfo(String event, String eventDesc, String data);
```

`调用示例`

```java

/**
 * 游戏埋点业务日志 
 */
private void reportGameLogToMgs(int roomType, int roomLimit) {

    try {
        //埋点参数
        JSONObject eventObject = new JSONObject();
        eventObject.put("room_type", roomType);
        eventObject.put("room_limit", roomLimit); 
        //上报日志
         MgsApi.getInstance().reportLogInfo("event_yc_enter_room", "进入房间事件",  eventObject.toString());
    } catch (JSONException e) {
        e.printStackTrace();
    } 
}
 
```

`请求参数`

根据运营提供的埋点方案进行传参


`返回值`

无



## 全局监听SDK通知事件

SDK提供全局监听函数，SDK内部会全局通知游戏需要做的操作。

API接口:

```java
/**
 * 注册全局的广播监听，用于SDK主动通知游戏
 * @param event 事件名称
 * @param listener 监听回调
 */
 public void registerMgsEventListener(String event, MgsEventListener listener)；
```

### 退出游戏事件通知

```java

MgsApi.getInstance().registerMgsEventListener("exitGameEvent", new MgsEventListener() {
    @Override
    public void onMgsEventHandle(String jsonData) {
        //退出游戏事件通知，游戏方可以做一些游戏清理工作，比如清理房间等
    }
});
```

`修改房间名称`: `废弃`

```java

MgsApi.getInstance().registerMgsEventListener("changeRoomNameEvent", new MgsEventListener() {
    @Override
    public void onMgsEventHandle(String jsonData) {
        //resultJson = {"roomIdFromCp":"1121","roomName":"修改后的昵称"}
        //同步到游戏服务器
    }
});
```


### MGS房间销毁通知

```java

MgsApi.getInstance().registerMgsEventListener("destroyRoomEvent", new MgsEventListener() {
    @Override
    public void onMgsEventHandle(String jsonData) {
        //处理房间销毁的操作
    }
});
```

## SDK销毁

SDK提供销毁方法，在游戏退出后可以销毁SDK，释放相应的内存。下次再调用其他接口需要再次进行初始化操作。

API接口：

```java
/**
 * SDK销毁事件
 */
void destroySdk();
```

示例代码:

```java
 MgsApi.getInstance().destroySdk();
```

**更多示例**

更多示例可参考DEMO中的`MgsSdkBridgeHelper`类。  
`MgsSdkBridgeHelper`是SDK提供的快速接入的封装，游戏方可直接使用或根据业务特征基于`MgsSdkBridgeHelper`类进行二次封装。


[1]:	#mgs%E5%AE%89%E5%8D%93sdk-api%E6%96%87%E6%A1%A3
[2]:	#%E6%8E%A5%E5%85%A5%E6%96%B9%E5%BC%8F
[3]:	#%E5%88%9D%E5%A7%8B%E5%8C%96
[4]:	#sdk%E5%8A%A8%E6%80%81%E5%8A%9F%E8%83%BD%E6%8E%A5%E5%8F%A3
[5]:	#%E7%99%BB%E5%BD%95
[6]:	#%E6%9F%A5%E8%AF%A2%E6%8E%A5%E5%8F%A3%E6%98%AF%E5%90%A6%E5%8F%AF%E7%94%A8
[7]:	#%E6%9F%A5%E8%AF%A2%E7%8E%A9%E5%AE%B6%E8%BF%9B%E5%85%A5%E6%88%BF%E9%97%B4%E6%97%B6%E7%9A%84%E6%93%8D%E4%BD%9C%E6%96%B9%E5%BC%8F
[8]:	#%E5%88%9B%E5%BB%BA%E6%88%BF%E9%97%B4
[9]:	#%E5%8A%A0%E5%85%A5%E6%88%BF%E9%97%B4
[10]:	#%E7%A6%BB%E5%BC%80%E6%88%BF%E9%97%B4
[11]:	#%E7%A6%BB%E5%BC%80%E6%88%BF%E9%97%B4
[12]:	#%E5%8A%A0%E5%85%A5%E9%98%9F%E4%BC%8D
[13]:	#%E7%A6%BB%E5%BC%80%E9%98%9F%E4%BC%8D
[14]:	#%E6%9F%A5%E7%9C%8B%E7%8E%A9%E5%AE%B6%E8%B5%84%E6%96%99%E5%8D%A1%E7%89%87
[15]:	#%E6%A3%80%E6%B5%8B%E7%8E%A9%E5%AE%B6%E6%98%AF%E5%90%A6%E6%98%AF%E5%A5%BD%E5%8F%8B%E5%85%B3%E7%B3%BB
[16]:	#%E6%B7%BB%E5%8A%A0%E5%A5%BD%E5%8F%8B-%E7%A4%BA%E4%BE%8B
[17]:	#%E6%98%BE%E7%A4%BA%E6%82%AC%E6%B5%AE%E7%AA%97
[18]:	#%E6%98%BE%E7%A4%BA%E6%B8%B8%E6%88%8F%E9%80%80%E5%87%BA%E7%A1%AE%E8%AE%A4%E6%A1%86
[19]:	#%E8%8E%B7%E5%8F%96%E5%BD%93%E5%89%8D233%E4%B9%90%E5%9B%AD%E7%8E%AF%E5%A2%83
[20]:	#%E6%97%A5%E5%BF%97%E4%B8%8A%E6%8A%A5
[21]:	#%E5%85%A8%E5%B1%80%E7%9B%91%E5%90%ACsdk%E9%80%9A%E7%9F%A5%E4%BA%8B%E4%BB%B6
[22]:	#%E9%80%80%E5%87%BA%E6%B8%B8%E6%88%8F%E4%BA%8B%E4%BB%B6%E9%80%9A%E7%9F%A5
[23]:	#mgs%E6%88%BF%E9%97%B4%E9%94%80%E6%AF%81%E9%80%9A%E7%9F%A5
[24]:	#sdk%E9%94%80%E6%AF%81