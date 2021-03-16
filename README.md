| 版本号 | 最后修改时间 |
| - | - |
| v1.0.0-210315 | 20210315-1712 |

# MGS技术文档

欢迎接入MGS! 本文档为MGS接入文档首页, 介绍MGS整体功能与结构.

MGS是MetaApp的游戏平台服务, 包含`MGS安卓SDK`和`MGS服务端`两部分. 通过接入MGS, 游戏将进驻233乐园联机大厅, 获取优质流量, 并能够与233乐园平台深度集成账号 / 好友 / 房间匹配, 获得开麦 / 邀请等能力.

本文档分为两大部分, MGS接入指南和MGS API文档. MGS接入指南会向您说明接入MGS服务的各个步骤, MGS API文档包含详细的接口信息.

MGS API文档则包含`MGS安卓SDK`和`MGS服务端`两部分

# 一. MGS接入指南

## 1. 开发者平台

如果已经完成过注册, 可以跳过此步骤.

- 首先进行[注册认证](https://dev.233leyuan.com/#/doc/9)
- 将`MGS安卓SDK`部分对接全部完成后, 进行[游戏上传](https://dev.233leyuan.com/#/doc/6)
- 请注意[合作须知](https://dev.233leyuan.com/#/doc/5)
- **TODO 20210224** MGS特有的开发者平台操作步骤, 设置字段, 设置语音频道和文字聊天维度. 这部分功能目前会由`MGS集成工程师操作`, 后续会开放UI页面

## 2. 对接成功自测, 样例游戏代码参考

接入完成后, 请参考[接入流程](https://dev.233leyuan.com/#/doc/1)按以下步骤自测.

- **TODO 20210224** 联运自测步骤, 这部分测试目前会由`MGS集成小队`配合进行
- **TODO 20210224** MGS自测步骤, 这部分测试目前会由`MGS集成小队`配合进行

## 3. MGS SDK简要流程

MGS提供轻量+可拓展的安卓SDK供游戏客户端使用,还提供服务端API, 对于一些特定接口, 由游戏服务端发起调用.
  
- **TODO 20210224** SDK会由`MGS集成工程师`提供, 形式是标准安卓aar文件

- MGS登录流程

![](https://cdn.233xyx.com/1614085716890_248.jpg)

- MGS房间操作流程

![](https://cdn.233xyx.com/1614085717113_485.jpg)


## 4. 分功能接入介绍

### 1. 账号登录

在233乐园环境中, 游戏客户端获取233乐园玩家的信息, 包括openId, 昵称, 游戏时长等. 游戏会绑定玩家的openId.

### 2. 好友邀请

游戏调用MGS安卓SDK接口, 来向其他玩家发送好友请求. 同时玩家可以邀请他们在233乐园中的好友, 来到您的游戏中一起游玩.

### 3. 房间匹配

房间部分是233乐园提供给游戏的重要流量入口和曝光位置. 对于游戏的房间模式, 有2大类: 普通房间模式和队伍房间模式. 另一个维度上, 对于游戏与MGS的接入方式上, 有同步房间和托管房间两种方式

#### 房间模式: 普通房间模式 Room

典型例子为斗地主, 象棋, 乱斗等. 核心特点是, 游戏建立的一个房间里面包含所有本局游戏的玩家, 开始后即玩.

此时, Room = 游戏建立的房间 = 233乐园联机大厅展示的房间.

#### 房间模式: 队伍房间模式 Party - Team - Room

**TODO 20210224** 本功能为二期功能, 目前尚未开放

典型例子为MOBA的组排 / 五黑, 核心特点是, 游戏建立的一个房间里面包含的是一个队伍的玩家. 准备开始后, 需要匹配另外一个 (或多个) 队伍. 匹配成功后所有队伍的人进入一个大房间, 开始游戏.

根据配置, 队伍或者是必须满员才能开始匹配, 或者是两人 / 三人后就可以开始匹配 (双排 / 三排场景).

此时, Group = 游戏建立的房间 = 233乐园联机大厅展示的房间.

匹配时, 一个或多个Group组成一个Team, 两个或多个Team构成一个Room, Room中的所有玩家参与一局游戏.

#### 接入方式: 同步房间

![](https://cdn.233xyx.com/1612448802357_421.png)

在这种方式下, 玩家在233乐园中点选创建房间 / 加入房间 / 快速开始时 (如上图), 会直接拉起游戏客户端. 游戏客户端会调用MGS安卓SDK获知用户的动作 (如加入A001房间, 创建房间, 快速匹配等), 然后做相应操作. 游戏现有的匹配逻辑可以全部保留, 不受影响. 当加入房间 / 创建房间等动作成功后 (游戏服务端会检查房间是否已满, 玩家等级是否可以加入该房间, 匹配玩家等级段位地区符合规则等等), 向MGS同步. MGS会更新233乐园联机大厅中的房间列表.

#### 接入方式: 托管房间

**TODO 20210224** 本功能尚未开放

### 4. 语音频道和文字聊天

![](https://cdn.233xyx.com/1612448289651_035.png)

语音频道和文字聊天呈现在233乐园悬浮层上. 游戏只需要配置是否需要语音频道和文字聊天, 语音频道和文字聊天放在哪个层级.

以MOBA 5v5举例, 语音频道和文字聊天可以存在于group层级 (也就是双排的两个人), 也可以存在于team层级 (也就是我方五人), 也可以存在于room层级 (全场10个人). 语音频道和文字聊天还可以关掉, 如果游戏特性决定不可以允许用户随时自由交流. 常见的, 会把语音频道和文字聊天都各只选一个层级配置. 如果比如文字聊天配置了多个层级, 玩家会需要在不同文字聊天室间来回切换发言.

### 5. 样例游戏服务端和样例游戏客户端

我们提供了已经完成集成的样例游戏服务端和样例游戏客户端, 勇闯233, 供参考

# 二. MGS API文档

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
 * @param feature 接口名称 ("login","queryPlayerAction","createRoom",etc.)
 * @param requestCode 请求码,游戏可根据业务特征来就进行区分是哪次请求,默认可传0
 * @param jsonParams  json格式参数
 * @param listener   回调监听
 */
void invokeFeature(String feature, int requestCode, String jsonParams, MgsFeatureListener listener);

```

feature接口列表描述:

`login` 登录  
`queryPlayerAction` 查询玩家操作方式  
`createRoom` 创建房间  
`joinRoom` 加入房间  
`leaveRoom` 离开房间  
`showUserProfile` 查看玩家资料信息  
`isFriendShip` 检测玩家是否好友关系  
`showFloatingLayer` 显示悬浮层(聊天:0 / 好友:1)  
 

**登录-示例**

游戏方在使用其他接口前，需要先调用`login`接口。

`调用示例`  

```java

//请求码,游戏可根据业务特征来就进行区分是哪次请求,默认可传0
int requestCode = 1000;

//调用登录接口
MgsApi.getInstance().invokeFeature("login", requestCode, null, new MgsFeatureListener() {
    @Override
    public void onSuccess(int requestCode, String resultJson) {

        //resultJson = {"openCode":"e1c575ca8a7732711e7a8c2f8b9f07dd","openId":"52767f66607dcea8558de6bccd7a270d"}
       
       if (requestCode == 1000) {
           //json格式转换,这里使用Gson进行转换，游戏可能就自身需求使用不同的转换工具进行转换
           Map<String, String> result = new Gson().fromJson(resultJson,
                new TypeToken<Map<String, String>>() {}.getType());

          //玩家openId
          String openId = result.get("openId");
          //玩家openCode
          String openCode = result.get("openCode");

          //通知游戏服务端登录结果
          requestLoginResultToServer(openId, code);
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
  "openCode":"e1c575ca8a7732711e7a8c2f8b9f07dd" //玩家openCode,用户有效性验证
}
       
```

**查询接口是否可用**  

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

**查询玩家进入房间时的操作方式-示例**  

游戏方可在玩家进入到游戏适当的时候调用该接口，查询玩家进入游戏时的操作方式，游戏方可根据操作进行相应的业务逻辑处理。  

`调用示例`

```java

//请求码,游戏可根据业务特征来就进行区分是哪次请求,默认可传0
int requestCode = 0;
//调用查询玩家进入房间的操作方式
MgsApi.getInstance().invokeFeature("queryPlayerAction", requestCode, null, new MgsFeatureListener() {
    @Override
    public void onSuccess(int requestCode, String resultJson) {
       // resultJson = {"roomIdFromCp":"158","action":0,"mgsCpRead":false}
       // action 玩家在233大厅选择进入房间时的操作方式,取值 0: 加入房间 1: 创建房间  2: 快速加入房间
      // roomIdFromCp 游戏同步给MGS的roomId,加入房间会有该值，其他action不会有
      // mgsCpRead 首次查询返回false，若退到后台再返回重新查询玩家操作，会返回true
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
"action":0,  //玩家在233大厅选择进入房间时的操作方式,取值 0: 加入房间 1: 创建房间  2: 快速加入房间
"roomIdFromCp": "123456", //游戏方房间号
"mgsCpRead":false //首次会返回false，若退到后台再返回重新查询玩家操作，会返回true，游戏根据业务进行处理。
} 
```

**创建房间-示例**  

游戏方创建好房间后可通过调用`createRoom`进行数据同步，也可通过MGS服务端进行数据同步。  
 
`调用示例`

```java

//请求参数
String params = "{\"roomIdFromCp\":\"1234\",\"roomName\":\"测试房\",\"roomLimit\":2}";

//请求码,游戏可根据业务特征来就进行区分是哪次请求,默认可传0
int requestCode = 0;
//创建房间
MgsApi.getInstance().invokeFeature("createRoom", requestCode, params, new MgsFeatureListener() {
            @Override
            public void onSuccess(int requestCode, String resultJson) {
              //创建房间成功
              //resultJson = {"roomIdFromCp":"1234","roomLimit":2,"roomName":"房间名称", "roomState":0, "roomShowNum": "100038"} 

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
  "roomShowNum": "100038"// 房间显示号
} 
```

**加入房间-示例**  

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
                 // resultJson = {"roomIdFromCp":"1234","roomLimit":2,"roomName":"房间名称", "roomState":0, "roomShowNum": "100038"} 
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
  "roomState":0,  //房间状态，取值（0: 可加入 1: 正在玩 2: 已销毁）
  "roomShowNum": "100038"// 房间显示号
} 
```

**离开房间-示例**  

游戏方在玩家加入离开房间，需要调用`leaveRoom`进行数据同步。  

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
                //resultJson = {"data": true}
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
  "data": true, //离开房间状态，true 成功  fase:失败 
}
```


**查看玩家资料卡片-示例**  

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

**检测玩家是否是好友关系-示例**  

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
                //成功回调
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

```java
{
  "data": true, //是否好友关系，true: 是好友  fase: 不是好友
}
```

**显示悬浮窗-示例**  

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

**显示游戏退出确认框-示例**  

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

`退出游戏事件通知`:  

```java

MgsApi.getInstance().registerMgsEventListener("exitGameEvent", new MgsEventListener() {
    @Override
    public void onMgsEventHandle(String jsonData) {
        //退出游戏事件通知，游戏方可以做一些游戏清理工作，比如清理房间等
    }
});
``` 

`修改房间名称`:  

```java

MgsApi.getInstance().registerMgsEventListener("changeRoomNameEvent", new MgsEventListener() {
    @Override
    public void onMgsEventHandle(String jsonData) {
        //resultJson = {"roomIdFromCp":"1121","roomName":"修改后的昵称"}
        //同步到游戏服务器
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

- 参数名ASCII码从小到大排序（字典序）；  
- 如果参数值为空不参与签名（运算）；  
- 参数名称大小写敏感（【参数名区分大小写】；  
- 验证调用时，传送的sign参数不参与签名，将生成的签名与该sign值作校验。
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

