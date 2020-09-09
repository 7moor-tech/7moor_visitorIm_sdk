# 客服IMSDK开发指南（Android）（3.2.9）

#客服IMSDK开发指南（Android）


[TOC]


##  一、sdk工作流程


![](https://cdn.nlark.com/yuque/0/2020/png/1289814/1588125992005-c7c616e0-24c4-4f34-b675-a5496188e8bc.png#align=left&display=inline&height=1350&margin=%5Bobject%20Object%5D&originHeight=1350&originWidth=1112&size=0&status=done&style=none&width=1112)


##  二、sdk下载文件说明
### 1、sdk文件包含内容：


- 所需jar包
- 录音所需so库（demo 中只含有部分主要平台）
- 对接说明文档
- 运行实例 demo



### 2、accessId获取方法


登录呼叫中心网站，点击左侧最下面的设置，选择左侧面板中的渠道设置，选择移动客服APP，点击添加按钮，即可创建一个应用的接入号。在创建好的应用点击配置说明可查看对应的accessId,用此ID即可初始化客服SDK。


![image-20190423142109268](/Users/zts/Library/Application Support/typora-user-images/image-20190423142109268.png)


### 3、运行KEFUDEMO项目：


- 该项目为android studio 项目，请用android studio进行打开
- 项目打开之后填写参数后可直接运行(参数修改文件为MainActivity)；



![](https://cdn.nlark.com/yuque/0/2020/png/1289814/1588125992002-324fddfe-bb8f-44e4-b800-b5a566af9bd5.png#align=left&display=inline&height=1046&margin=%5Bobject%20Object%5D&originHeight=1046&originWidth=750&size=0&status=done&style=none&width=750)


```
helper.initSdkChat("请填写后台获取的accessId", "hhtest", "userId");
```


(填写获取的accessId后可直接运行demo,查看效果)


**提醒：accessId + userId +设备号 是访客的唯一标识。**


##  三、开始集成
### 1、将所提供demo 中的 imkf 当作一个module引入到自己的项目中
![image.png](https://cdn.nlark.com/yuque/0/2020/png/1289814/1596188141476-fdb4ddc0-f980-4b50-aa17-f4a8f1e97935.png#align=left&display=inline&height=199&margin=%5Bobject%20Object%5D&name=image.png&originHeight=398&originWidth=646&size=37519&status=done&style=none&width=323)
具体步骤
1:使用自己的Android studio导入Module
![image.png](https://cdn.nlark.com/yuque/0/2020/png/1289814/1596187045282-0580c29b-ffc3-4e13-9cb3-b7cf2cfaf2ea.png#align=left&display=inline&height=202&margin=%5Bobject%20Object%5D&name=image.png&originHeight=404&originWidth=1258&size=641861&status=done&style=none&width=629)
2:在窗口中选择到 ykfsdk的 modlue中，进行下一步导入。
![image.png](https://cdn.nlark.com/yuque/0/2020/png/1289814/1596187840430-868d01b2-2446-4017-be84-dc42f1c12727.png#align=left&display=inline&height=385&margin=%5Bobject%20Object%5D&name=image.png&originHeight=1280&originWidth=1852&size=116546&status=done&style=none&width=557)
3:在自己项目app目录下的build.gradle中增加：
       implementation project(path: ':ykfsdk')


4:重新 build你的项目。




备注：


- 如果没有特定场景需求，本demo示例包含大部分需求，用户将demo导入自己项目后，输入accessid后就可完成
- **_特别注意（NewMsgReceiver 这个类的路径必须是 com.m7.imkfsdk.receiver，否则会导致无法及时收到消息）_**
**注意：sdk初始化可以直接参考demo中MainActivity的代码。**



```java
先申请权限 android 版本>=6.0 代码也在MainActivity 
/**
 * 第一步：初始化help 文件
 */
final KfStartHelper helper = new KfStartHelper(MainActivity.this);
//点击跳转在线咨询的按钮（换成自己按钮监听）
findViewById(R.id.button).setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
         helper.initSdkChat("fa0f5110-82ec-11e7-8ca1-8b4900b05172", "测试2","97289000");
            }
        });
```


- 如果有关于头像、商品链接、扩展信息字段请参考本文章中的延伸功能
- 以下接口说明是针对特殊场景开发以及向开发人员说明接口功能



### 2、接口说明(适应于自定义场景)


#### 初始化SDK


```java
IMChatManager.getInstance().init(getApplicationContext(),  
             "receiverAction", 
             "accessId",    
             "username ",  
             "userId");
```


其中参数说明：
Context context, 应用上下文.
String receiverAction 注册接收消息广播的action，填写自己定义的，该值也得填写到. AndroidManifest.xml中的NewMsgReceiver中的action中。
String accessId, 接入号,必填项.
String username, 用户名，用来在后台显示.
String userId,   用户id，用来标识用户.


#### SDK初始化的接口监听


```java
MChatManager.getInstance().setOnInitListener(new InitListener() {
        @Override
        public void oninitSuccess() {
            LogUtil.d("MobileApplication", "sdk初始化成功");
        }
        @Override
        public void onInitFailed() {
            LogUtil.d("MobileApplication", "sdk初始化失败");
        }
    });
```


注意：该回调接口只是用来判断SDK是否初始化成功了，注意：只有成功了之后才可以使用IM相关功能,该返回接口是在主线程中，可以直接操作UI


#### 获取配置日程管理接口.
开始会话前先得获取是否配置日程，代码如下：
具体使用详见demo


```java
IMChatManager.getInstance().getWebchatScheduleConfig(InfoDao.getInstance().getConnectionId(), new GetGlobleConfigListen() {
            @Override
            public void getSchedule(ScheduleConfig sc) {
                
            }
            @Override
            public void getPeers() {
                
            }
 });
```


#### 获取技能组接口


开始会话前先得获取后台配置的技能组，代码如下：


```java
IMChatManager.getInstance().getPeers(new GetPeersListener() {
            @Override
            public void onSuccess(List<Peer> peers) {
            }
            @Override
            public void onFailed() {
            }
 });
```


onSuccess接口中返回的即是技能组的数据列表


#### 会话开始接口


当开始一次新会话时，需请求该接口通知服务器，并将需要联系的技能组id传进来，代码如下:


```java
IMChatManager.getInstance().beginSession(peerId, new OnSessionBeginListener() {
			@Override
			public void onSuccess() {
			}
			@Override
			public void onFailed() {	
			}
});
```


该接口的回调中
onSuccess表示会话开始成功了
onFailed表示接口请求失败，具体请参考提供的demo。


#### 广播接收器


调用过该接口后，需在聊天界面中注册广播来接收当前是机器人还是人工客服的状态，进行界面的相应变化。广播接收器代码如下：


```java
class KeFuStatusReceiver extends BroadcastReceiver {
		@Override
		public void onReceive(Context context, Intent intent) {
			String action = intent.getAction();
			if(IMChatManager.ROBOT_ACTION.equals(action)) {
				//当前是机器人
				handler.sendEmptyMessage(0x111);
			}else if(IMChatManager.ONLINE_ACTION.equals(action)) {
				//当前客服在线
				handler.sendEmptyMessage(0x222);
			} else if(IMChatManager.OFFLINE_ACTION.equals(action)) {
				//当前客服不在线
				handler.sendEmptyMessage(0x333);
			}
		}
	}
```


具体请参看demo；
#### 获取后台满意度是否开启
获取后台满意度是否开启的配置
在调用完beginSession成功后，可以通过.


```
IMChatManager.getInstance().isInvestigateOn()
```


方法来获取满意度评价是否开启，来隐藏或显示评价按钮。


#### 转人工客服


当服务器配置了机器人聊天后，通过转人工接口可以与人工客服进行聊天，代码如下：


```java
IMChatManager.getInstance().convertManual(new OnConvertManualListener() {
				@Override
				public void onLine() {
					//有客服在线，隐藏转人工按钮
					Toast.makeText(ChatActivity.this, "转人工服务成功", Toast.LENGTH_SHORT).show();
				}

				@Override
				public void offLine() {
					//当前没有客服在线，弹出离线留言框 
				}
});
```


成功之后，则可以与人工客服进行聊天。


#### 提交离线留言
当客服不在线时，可以提交离线留言，代码如下:


```java
IMChatManager.getInstance().submitOfflineMessage(peerId, content, phone, email, new OnSubmitOfflineMessageListener() {
                            @Override
                            public void onSuccess() {
                                dismiss();
                                Toast.makeText(getActivity(), "提交留言成功", Toast.LENGTH_SHORT).show();
                            }
                            @Override
                            public void onFailed() {
                                dismiss();
                                Toast.makeText(getActivity(), "提交留言失败", Toast.LENGTH_SHORT).show();
                            }
 });
```


提交离线留言中参数peerId为技能组的id, content为留言内容,phone是电话,email是邮箱。


#### 消息实体


界面显示时会用到消息的一些属性进行不同的显示，下面将消息中的具体属性展示如下：
消息实体类为FromToMessage.

| 属性 | 说明 |
| :---: | :---: |
| userType | 用户发送消息的类型：发送的消息为”0”,接收的消息为”1” |
| msgType | 消息类型：目前有4种，分别为文本消息，录音消息，图片消息,  知识库文本 |
| when | 消息发送或接收的时间 |
| message | 消息的文本内容 |
| sendState | 消息发送的状态 |
| recordTime | 若为录音消息，表示录音时长 |
| filePath | 录音文件或图片文件在本地的路径 |



更多详细请查看demo


#### 拼装一条消息


- 文本消息：



使用如下代码


```
FromToMessage fromToMessage = IMMessage.createTxtMessage(txt);
```


参数说明：
String txt, 消息文本内容.


- 录音消息:



使用如下代码.


```
FromToMessage fromToMessage = IMMessage.createAudioMessage(mTime, filePath);
```


参数说明：
float mTime, 录音时长
String filePath, 录音在本地的路径


- 图片消息:



使用如下代码


```
FromToMessage fromToMessage = IMMessage.createImageMessage(picFileFullName);
```


参数说明：
String picFileFullName, 图片在本地的路径


- 文件消息：



```
FromToMessage fromToMessage = IMMessage.createFileMessage(path, fileName, fileSizeStr);
```


参数说明：
String path, 文件在本地的路径
String fileName, 文件名字
String fileSizeStr, 文件大小
拼装好的消息在发送时用到。


#### 发送消息


使用如下代码:


```java
IMChat.getInstance().sendMessage(fromToMessage, new ChatListener() {
			@Override
			public void onSuccess() {	
			}
			@Override
			public void onFailed() {	
			}
			@Override
			public void onProgress() {
			}
		});
```


参数说明：
FromToMessage fromToMessage, 要发送的消息
ChatListener ,消息发送的接口监听，发送成功，失败或正在发送，该回调接口中可以直接进行界面的操作。发送的消息存到了本地数据库中，具体可参看提供的demo


#### 录制MP3格式语音


由于跨平台的需要，发送的录音文件格式需为mp3格式，因此提供了录制mp3格式的方法，代码如下：


```java
MP3Recorder mp3Recorder = new MP3Recorder(file);//初始化录音器
mp3Recorder.start();//开始录制
mp3Recorder.stop();//结束录制
```


其中的参数为：File file录音保存文件,具体使用可参看提供的demo


#### 重发消息
当有时候发送失败后，需重新发送该条消息，代码如下：


```java
IMChat.getInstance().reSendMessage(fromToMessage, new ChatListener() {
			@Override
			public void onSuccess() {
			}
			@Override
			public void onFailed() {
			}
			@Override
			public void onProgress() {
		   }
		});
```


#### 接收新消息


需通过注册广播来获取新消息，首先需要一个全局注册的广播，在AndroidManifest.xml中代码为：


```xml
<receiver
       android:name="com.m7.imkfsdk.receiver.NewMsgReceiver"
       android:enabled="true"
       >
    <intent-filter android:priority="2147483647" >
    <!—修改此action为自己定义的，该action必须和SDK初始化接口中所填的一样,举例如下-->
     <action android:name=“com.m7.demo.action” />
     </intent-filter>
</receiver>
```


请先修改此action的值，和SDK初始化时传入的值必须相同，否则接收不到新消息的广播，注意当接收到该广播后，消息已经保存到了本地的数据库中了。具体请查看demo。


#### 获取数据库中的消息
在界面上显示消息就得先从数据库中获得消息数据，代码如下：


```
List<FromToMessage>fromToMessages=IMChatManager.getInstance().getMessages(1);
```


参数中的数字为取第几页的数据，用于下拉加载更多消息时使用,默认是一页15条消息数据。这样就获取到了数据库中的消息了，之后就可以在界面进行显示操作了。具体参考demo中。


#### 更新一条消息数据到数据库中


若需要将消息的数据修改后保存到数据库中，代码如下：


```
IMChatManager.getInstance().updateMessageToDB(message);
```


参数为FromToMessage message,修改数据后的消息。


#### 判断数据库中数据是否都取出
界面显示时需要判断本地数据库中的数据是否已经被全部取出，代码如下：


```
IMChatManager.getInstance().isReachEndMessage(size);
```


#### 获取评价列表数据


获取后台配置的评价列表数据，代码如下：


```java
List<Investigate> investigates = IMChatManager.getInstance().getInvestigate();
```


返回的结果结果为：List investigates，即为评价实体的列表，Investigate中的name属性为该评价的名称，可用来显示在界面上。


#### 提交评价结果
代码如下：


```java
IMChatManager.getInstance().submitInvestigate(investigate, new SubmitInvestigateListener() {
             @Override
             public void onSuccess() {
     Toast.makeText(InvestigateActivity.this, "评价提交成功", Toast.LENGTH_SHORT).show();
                    }
             @Override
             public void onFailed() {
     Toast.makeText(InvestigateActivity.this, "评价提交失败",Toast.LENGTH_SHORT).show();
                    }
});
```


参数为：
Investigate investigate 评价实体
SubmitInvestigateListener 提交评价回调接口
注：该接口只有在与人工客服聊天过后评价才有意义。


#### 客服主动发起的评价


客服在服务完客户后也可以主动发起评价的请求，客户端需要作出相应的响应，客户端在聊天界面注册相应的广播，接收到相应的事件会生成一个评价的消息，点击完评价之后该消息会被删除，生成评价消息的代码如下：


```java
private void sendInvestigate() {
	ArrayList<Investigate> investigates = (ArrayList<Investigate>) IMChatManager.getInstance().getInvestigate();
	IMMessage.createInvestigateMessage(investigates);
	updateMessage();
}
```


#### 工号、姓名、头像推送


在接入聊天会话后，会把客服人员的工号、姓名和头像信息推送回来。聊天界面中通过接收IMChatManager.USERINFO_ACTION的广播来获取对应数据。


#### 访客无响应断开对话时长配置获取


通过GlobalSet globalSet = GlobalSetDao.getInstance().getGlobalSet();来获取配置信息，里面的break_len代表断开对话时长，break_tips_len代表断开前提示时长，break_tips代表提示内容。


#### 注销


若需注销当前用户，则可以调用该接口，代码如下：


```java
IMChatManager.getInstance().quit();
```


#### 设置IP地址(私有云用户)
若需设置自己服务器的地址（在初始化init()之前调用以下方法），代码如下：


```java
RequestUrl.setRequestUrl()
(int tcpPort, String tcpHost, String http1, String http2,String wsAddress)
    参数说明：tcpPort：tcp使用的端口号
             tcpHost：tcp使用的服务地址
             http1，http2：使用的Http服务
             wsAddress：使用到的websocket 域名和端口号例如：117.15.85.141:7073，
                        如不需要使用传空字符串即可。
```


#### 开启关闭log打印


sdk中包含帮助开发人员开发的数据log,上线时可根据需求开关，代码如下：


```java
 KfStartHelper helper = new KfStartHelper(MainActivity.this);
 //关闭log
 helper.closeLog();
 //打开log
 helper.openLog();
```


#### 混淆


混淆时加上如下代码：


```java
-keep class com.moor.imkf.** { *; }

-keepclassmembers class ** {
    public void onEvent*(**);
}
```


shrinkResources设置为false，不然表情显示不正确


## 四、延伸功能


##### 1、如需自定义用户头像和机器人头像


![](https://cdn.nlark.com/yuque/0/2020/png/1289814/1588125992040-c476aa4c-fc52-48f0-bcf3-303a0b66024b.png#align=left&display=inline&height=1060&margin=%5Bobject%20Object%5D&originHeight=1060&originWidth=2002&size=0&status=done&style=none&width=2002)


##### 2、设置专属座席功能


调用beginSession的重载方法，可传入json字符串，示例如下，需在 ChatActivity 中对原有的 beginSession 进行修改，不可单独调用


```java
String otherParams = "";
JSONObject jsonObject = new JSONObject();
try {
    jsonObject.put("agent", "8131");
} catch (JSONException e) {
    e.printStackTrace();
}
otherParams = jsonObject.toString();
IMChatManager.getInstance().beginSession(peerId, otherParams, new OnSessionBeginListener() {})
```


##### 3、设置扩展信息


调用beginSession的重载方法，可传入json字符串，示例如下，需在 ChatActivity 中对原有的 beginSession() 和beginScheduleSession()进行修改，不用关注beginsession代码里执行了几次，只增加


```java
        //JSONObject j1中可配置您的想要添加的扩展字段,
		//key与value根据需求要自定义。
		JSONObject j1 = new JSONObject();
        j1.put("name","测试扩展字段");
        j1.put("other","前面字段可以自定义");
            
		//将j1 添加到JSONObject j2中，注意 j2段代码必须是如下配置。
        //不可修改与增加j2 中的key value。直接复制即可。
        JSONObject j2 = new JSONObject();
        j2.put("customField",URLEncoder.encode(j1.toString()));
        j2.toString()；
        
    
```


将j2.toString() 得到的String 分别添加到：
1:ChatActivity中的beginSession() 方法 添加：
![image.png](https://cdn.nlark.com/yuque/0/2020/png/1289814/1596430254639-f3cc8420-6807-425f-b9a0-79e5a0db88ad.png#align=left&display=inline&height=363&margin=%5Bobject%20Object%5D&name=image.png&originHeight=1162&originWidth=1950&size=324920&status=done&style=none&width=610)


2:ChatActivity中的beginScheduleSession() 方法 添加：
![image.png](https://cdn.nlark.com/yuque/0/2020/png/1289814/1596430328107-b9a84afa-7dea-4585-bc8b-b088d0e96eac.png#align=left&display=inline&height=228&margin=%5Bobject%20Object%5D&name=image.png&originHeight=824&originWidth=2256&size=259223&status=done&style=none&width=623)


**注意 ： 设置扩展信息一定要确保会话是一次全新的会话，即第一次进入会话设置才会有效。**


##### 4、发送商品链接


在初始化之前调用setCard(new CardInfo())


查看示例代码


```java
  CardInfo ci = new CardInfo("图片url", "第一行内容", "第二行内容", "第三行内容", "点击跳转链接");
  //设置card
  helper.setCard(ci);
  //设置参数初始化
  helper.initSdkChat("com.m7.imkf.KEFU_NEW_MSG", 
                  "请填写后台获取的accessId", 
                   "hhtest", 
                   "userId");
```


如果商品信息中包含了特殊字符的话，特殊字符的地方要加上转义，为了兼容所有字段可能出现特殊字符，全部增加转码如下,为了前端可以正常展示，前端要相应的转码


```java
try {
      ci = new CardInfo(URLEncoder.encode(icon, "utf-8"), URLEncoder.encode(title, "utf-8"), URLEncoder.encode(content, "utf-8"), URLEncoder.encode(rigth3, "utf-8"),
                            URLEncoder.encode(s, "utf-8"));
                } catch (Exception e) {
                    e.printStackTrace();
                }
                //设置card
                helper.setCard(ci);
```


##### 5、开启后主动会话（后台选择sdk模式）


- 关闭会话依旧保持长连接(demo中为R.id.chat_tv_back的注销按钮,以及 onBackPressed 事件)
```imchatmanager.getinstance
IMChatManager.getInstance().onLineQuitSDK();
```

- 获取未读消息数
```
int unReadCount = IMChatManager.getInstance().getMsgUnReadCount();
```


注意：如果选用sdk的主动回话功能，因为安卓没有统一的离线推送机制，本解决方案只是在用户注销时，其实后台长连接进程还在，短时间坐席发送的消息可以收到，时间一长或者app重启，休眠都会造成后台进程断开；（用户可以采用url推送，借助app的推送机制进行推送消息（eg:华为离线推送、小米离线推送等））


##### 6、本sdk支持国际英文版


![](https://cdn.nlark.com/yuque/0/2020/png/1289814/1588125992042-ecfd2446-9055-4d0c-8a3d-4a26f7830d0f.png#align=left&display=inline&height=942&margin=%5Bobject%20Object%5D&originHeight=942&originWidth=654&size=0&status=done&style=none&width=654)


- 如需移植到项目中，请将values-en下的string.xml复制到项目中该文件下
- 若需切换可根据需求在界面加载之前（全局可在项目application类中）执行以下代码：
```java
//加载默认 中文
initLocaleLanguage（“”）;
//记载英文
initLocaleLanguage（“en”）;

private void initLocaleLanguage(String language) {
        Resources resources = getApplicationContext().getResources();
        Configuration configuration = resources.getConfiguration();
        configuration.locale = new Locale(language);
        resources.updateConfiguration(configuration, resources.getDisplayMetrics());//更新配置
    }
```


##### 7、聊天关闭后获取消息未读数


- 首先判断的是sdk是否初始化成功过，因为获取消息数需要userid等参数，只有在初始化之后才会有这些参数



```java
//首先判断是否在此之前初始化过sdk,获取消息参数需userid等参数
 if (MoorUtils.isInitForUnread(MainActivity.this)) {
 IMChatManager.getInstance().getMsgUnReadCountFromService(new     
 IMChatManager.HttpUnReadListen() {
                        @Override
 public void getUnRead(int acount) {
 Toast.makeText(MainActivity.this, "未读消息数为：" + acount, Toast.LENGTH_SHORT).show();
 }});
 } else {
 //未初始化，消息当然为 ：0
 Toast.makeText(MainActivity.this, "还没初始化", Toast.LENGTH_SHORT).show();}
```


## 
## 五、常见问题处理


##### 1、点击录音按钮异常或者初始化报关于.so的错误


可能原因：一般是因为每个平台下的so库数量、种类不统一，只要保证每个平台下的so库数量一致，即可决绝问题;
解决：根据项目原有目录添加对应so库


           资源：录音相关的  （[点击下载so库](https://fs-kefu-sdk.7moor.com/moormp3lib.zip)）


                       视频相关的，只针对于视频版sdk,普通版sdk忽略（[点击下载so库](https://fs-kefu-sdk.7moor.com/video_jar_so.zip)）


注：如果发现以上操作都处理了，目录也一致了，依然报.so 错误，需要把自己app打一个apk文件放桌面，解压一下，解压后查看下lib目录下.so目录是否跟代码中一致。不一致，按照lib上的目录，改成一致的。添加相应的.so


##### 2、消息接收不显示，但在发送消息后同时显示之前接收的消息


可能原因：


检查com.m7.imkfsdk.receiver.NewMsgReceiver的文件是否和demo中的文件包名是否一致（因jar中广播指向的是该包名下的NewMsgReceiver，android8.0以后广播需携带包名）


##### 3、客服im退出后，再次进入客服聊天界面异常、闪退


可能原因：在点击退出按钮是未添加退出sdk代码.


```java
//退出聊天界面就注销了SDK，若不需要注销则把该处代码去掉
IMChatManager.getInstance().quit();
IMChatManager.isKFSDK = false;
```


##### 4、sdk集成后表情显示不出来


原因：1、assets文件下表情对应关系 emojikf不存在


          2、未添加表情初始化代码


解决：将demo里的emojikf文件拷贝到项目中该目录下、添加初始化代码·


```java
//初始化表情
new Thread(new Runnable() {
            @Override
            public void run() {
com.m7.imkfsdk.utils.FaceConversionUtil.getInstace().
            getFileText(getApplicationContext());
            }
 }).start();
```


##### 5、商品发送链接客户端发送出去pc坐席端收不到


解决：将发送点击链接进行utf-8转码


```java
  CardInfo ci = null;
try {
   ci = new CardInfo("http://seopic.699pic.com/photo/40023/0579.jpg_wh1200.jpg",
                     "我是一个标题当初读书", 
                     "我是name当初读书。", 
                     "价格 1000-9999", 
                      URLEncoder.encode(s, "utf-8"));
 } catch (Exception e) {
                    e.printStackTrace();
                }
  helper.setCard(ci);
```
## 六、项目迁移到AndroidX方法
本SDK中目前使用的是Android support库，以更好的兼容更多版本 Android开发与运行环境，如您的主项目为Android X库并需要将本SDK迁移至Android X库版本，请参照以下升级方法。
##### 具体参考：  （[迁移参考（来源于简书](https://www.jianshu.com/p/7dc111353328)）
**1）更新升级AS以及Gradle插件**

- 将AS更新至 **AS 3.2**及以上；
- Gradle 插件版本改为 **4.6**及以上；
项目下 `gradle/wrapper/gradle-wrapper.propertie` 文件中的`distributionUrl`改为：



```
distributionUrl=https\://services.gradle.org/distributions/gradle-4.6-all.zip
```

- compileSdkVersion 版本升级到 **28**及以上；
- buildToolsVersion 版本改为 **28.0.2**及以上。

![](//upload-images.jianshu.io/upload_images/4625401-92ed6de990f27533.png?imageMogr2/auto-orient/strip|imageView2/2/w/546/format/webp#align=left&display=inline&height=261&margin=%5Bobject%20Object%5D&originHeight=261&originWidth=546&status=done&style=none&width=546)
插件更新提示
**2）开启迁移AndroidX**
 在项目的`gradle.properties`文件里添加如下配置：


```
android.useAndroidX=true
android.enableJetifier=true
```
 表示项目启用 AndroidX 并迁移到 AndroidX。
**3）一键迁移AndroidX库**
 AS 3.2 及以上版本提供了更加方便快捷的方法一键迁移到 AndroidX。选择菜单上的**ReFactor —— Migrate to AndroidX...** 即可。（如果迁移失败，就需要重复上面1，2，3，4步手动去修改迁移）
![](//upload-images.jianshu.io/upload_images/4625401-b9524e8fa789d620.png?imageMogr2/auto-orient/strip|imageView2/2/w/316/format/webp#align=left&display=inline&height=423&margin=%5Bobject%20Object%5D&originHeight=423&originWidth=316&status=done&style=none&width=316)
AndroidX 迁移
**注意：**如果你的项目compileSdkVersion 低于28，点击Refactor to AndroidX...会提示：


```
You need to have at least have compileSdk 28 set in your module build.gradle to refactor to androidx
```
提示让你使用不低于28的sdk，升级最新到SDK，然后点击 **Migrate to AndroidX...**，AS就会自动将项目重构并使用AndroidX库。
##  
## 七、版本说明


#### 3.2.9 （2020.9.04）更新日志

- 新增xobt机器人消息支持视频类型。
- 新增xbot机器人消息支持电话号类型点击。
- 新增xbot机器人消息支持配置转人工类型文字按钮。
- 支持使用websocket服务连接。
- 发送文件大小提高至200Mb。



#### 3.2.8 （2020.8.03）更新日志

- 新增接收的消息消息文本中如包含电话号码，可以点击复制或呼叫。
- 优化满意度评价窗口样式，文字长度最大限制50字。
- 新增点击注销按钮满意度弹窗，支持PC端后台控制。
- 新增支持用户在pc端对点赞点踩自定义文案展示。
- 新增支持用户在pc端自定义选择xbot消息展示的点选样式。
- 优化sdk体验及修复若干已知bug。



#### 3.2.7 （2020.6.30）更新日志

- 新增语音转文本功能
- 新增支持坐席配置是否允许访客发起评价
- 新增系统头像显示
- 优化xbot机器人自动转人工不在线的留言提示
- 修复特殊机型或特殊屏幕设置导致评价窗口展示不完整可滑动展示
- 修复专属坐席不在线转其他坐席逻辑
- 修复sdk中已知bug



#### 3.2.6 （2020.4.29）更新日志

- 适配Android Q版本
- 添加无技能组提示
- 修复键盘弹出不隐藏加号布局bug



#### 3.2.6更新日志

- 新增xbot卡片消息
- 新增满意度评价的时效性、必填项功能
- 新增常见问题功能
- 修复sdk转人工按钮显示异常bug
- 修复文件消息-文件名展示异常bug



#### 3.2.5更新日志

- 优化Android10系统获取设备Id
- xBot机器⼈,增加底部滑动提示输入信息
- 优化某些机型选择图⽚后无法发送的问题
- 优化转人工按钮显示逻辑



#### 3.2.4更新日志

- 某些机型大图预览展示不完整问题
- xbot2 机器人问题列表点选功能
- 修复商品链接不跳转的问题
- 修正SDK所有接口请求成功后返回值类型
- 某些机型发送txt文件会闪退
- 修复某些机型LogUtils 初始化闪退的问题
- glide版本 增加4.9兼容代码
- 兼容编译版本27+



#### 3.2.3更新日志

- 增加了xbot机器人功能
- 修复了机器人关联问题的bug



#### 3.2.2更新日志

- 双网络切换导致tcp链接出现的问题
- 网络监听，校验优化
- 增加图片长按复制功能
- 增加文本长按复制功能
- 满意逻辑重新调整，优化
- 小陌机器人头像展示pc端设置
- SDK在开启会话接口（beginSession）执行返回成功之后才能发送消息
- 增加日程情况下也可以支持商品信息



#### 3.2.0更新日志

- 小陌机器人的回答中增加有无帮助数的选择和感谢文案
- 消息模型增加robotMsgId字段，给小陌报表查看会话时调用数据
- 小陌机器人显示满意度评价, 其他机器人不展示满意度评价按钮，只有机器人说话后才可以评价，且一个会话只能评价一次，不能重复评价
- 座席的满意度评价界面改版，增加二级评价标签，评价逻辑维持原来的不变
- 支持自定义排队时文案提示
- 线上问题处理：增加推送activeClaim字段的处理，部分机型语音发送有问题(如:小米Mix2)
- 优化断线重连，提升稳定性
- 修复用户断线之后，坐席结束会话导致的异常
- 部分机型录制语音出现的bug修复
- 会话频繁重连接入，导致会话生成多个，并且不能离线
- 修复android版本低于6.0会直接闪退，打不开demo
- 修复小陌机器人会一直重复推送提示消息，直到app死掉
- 修复app长期处于后台切换为前台时访客无法在线问题



#### 3.1.0更新日志

- 在线客服连接时增加机型、SDK 版本号等字段
- 优化设备 ID 的获取
- 帮助评价文案修改



#### 3.0.0更新日志


- TCP 与 HTTP 服务器地址动态切换以及重连
- 录音无法播放 bug 修复
- 优化集成步骤
#### 2.8.4更新日志


- 转人工按钮的显示配置
- 留言和满意度文案的配置
- 访客回复消息再接入会话配置
- 优化部分体验



#### 2.8.3更新日志

- 优化消息数据库存取标记（适配支持useid和peedid自有选择）
- 调整在线客服入口文件（隐藏广播action）
- 修复获取聊天未读消息数的异常
- 优化部分体验



#### 2.8.2更新日志

- 修复文件下载和文件打开版本适配问题
- 修改消息记录显示以userId的依据（同一userId的用户可以看见聊天记录）
- 优化部分代码、修复已发现bug



#### 2.8.1更新日志

- 调整代码入口代码
- 调整录音、照相权限申请位置
- 添加商品链接功能
- 修复android8.0以后广播和通知接受异常



#### 2.8.0更新日志

- 添加主动会话功能
- 优化部分代码
- 修复一发现bug



#### 2.7.1更新日志

- 支持国际英文版
- 优化部分代码
- 修复已发现异常



#### 2.7.0更新日志

- 取消demo里面关于application 的部分（demo中MobileApplication文件）
- 之前所有的 MobileApplication.isSDK 的判断全改为IMChatManager.isKFSDK
- 将之前MobileApplication类中的 表情的初始化 移到 MainActivty 类
- 优化部分代码
- 修复发现bug
- 删除多余文件，减少体积
