# 目录结构

├── Resources								//资源目录

├── home-page								//首页

├── components							//组件

│   └── mlvb-live-room					//直播组件

├── live-player-demo					//播放页面

├── rtc-player-demo						//低延时播放页面

├── live-pusher-demo					//推流页面

│   └── push-config						//推流设置页面

├── mlvb-live-room-demo					//使用live-room的demo示例

│   ├── live-room-page					//房间页面

│   ├── room-list-page					//房间列表页面

│   └── new-room-page					//新建房间页面

└── readme.md								//文档

>注意：如需使用 “腾讯视频云” 小程序中的<font color=red> “视频通话” </font>组件，请查看 [TRTCSDK](https://github.com/tencentyun/TRTCSDK/tree/master/WXMini)


# 组件使用说明
## mlvb-live-room组件

### 属性定义

**&lt;mlvb-live-room&gt;** 组件包含如下属性：

| 属性      | 类型    | 值           | 说明       |
|:---------:|:---------:|:---------:|--------------|
| template  | String  | 'float'  | 必要，标识组件使用的界面模版。 |
| roomID    | String  | 您来指定               | 可选，房间号（role = audience 时，roomID 不能为空）。    |
| roomName  | String  | 您来指定            | 可选，房间名。  |
| role         | String  | ‘anchor’，'audience' | 必要，anchor 代表主播，audience 代表观众。 |
| pureaudio | Boolean | true，false             | 可选，默认 false，是否开启纯音频推流。               |
| beauty    | Number  | 0 - 5                     | 可选，默认5，美颜级别0 - 5。  |
| muted     | Boolean | true，false             | 可选，默认 false，是否静音。    |
| debug     | Boolean | true，false             | 可选，默认 false，是否打印推流 debug 信息。   |
| bindonRoomEvent     | function |   -           | 必要，监听 mlvb-live-room 组件返回的事件。   |



### 操作接口

**&lt;mlvb-live-room&gt;** 组件包含如下操作接口，您需要先通过 selectComponent 获取 &lt;mlvb-live-room&gt; 标签的引用，之后就可以进行相应的操作。

| 函数名                                          | 说明         |
|--------------------------------------------|--------------|
| start()                                         | 启动。     |
| pause()                                       | 暂停。     |
| resume()                                     | 恢复。    |
| stop()                                          | 停止。     |
| requestJoinAnchor()                                              | 请求连麦，适用于 audience。  |
| respondJoinAnchor(agree:Boolean, audience:Object) | 同意连麦，适用于 anchor。  |
| switchCamera()                           | 切换摄像头。   |
| sendTextMsg(text:String)             | 发送文本消息。 |

```
var liveroom = this.selectComponent("#id_liveroom");
liveroom.pause();
```

### 事件通知
**&lt;mlvb-live-room&gt;** 标签通过 **onRoomEvent** 返回内部事件，事件参数格式如下：

```json
"detail": {
  "tag": "事件tag标识，具有唯一性",
  "code": "事件代码",
  "detail": "对应事件的详细参数"
}
```

### 示例代码
```
// Page.json 文件
"usingComponents": {
    "mlvb-live-room": "/pages/components/mlvb-live-room/mlvbliveroomview"
}

// Page.wxml 文件
<mlvb-live-room id="id_liveroom"
	roomID="{{roomID}}"
	roomName="{{roomName}}"
	template="float"
	beauty="{{beauty}}"
	muted="{{muted}}"
	debug="{{debug}}"
	bindonRoomEvent="onRoomEvent">
</mlvb-live-room>


// Page.js 文件
Page({
    data: {
    	//...
        roomID: '',
        roomName: '',
        beauty: 3,
        muted: false,
        debug: false,
    },
    //...
    onRoomEvent: function(e){
        switch(e.detail.tag){
            case 'roomClosed': {
                //房间已经关闭
                break;
            }
            case 'error': {
                //发生错误
                var code = e.detail.code;
                var detail = e.detail.detail;
                break;
            }
            case 'recvTextMsg': {
                //收到文本消息
                var text = e.detail.detail;
                break;
            }
            case 'requestJoinAnchor': {
                //主播收到来自观众的连麦请求
                var audience = e.detail;
                var name = audience.userName;
                var id = audience.userID;
                // 允许请求
                liveroom.respondJoinReq(true, audience)
                break;
            }
            case 'linkOn': {
                //连麦观众在连麦成功时会收到此通知
                break;
            }
            case 'linkOut': {
                //连麦观众退出连麦时会收到此通知
                break;
            }
        }
    },

  onShow: function () {
  },

  onHide: function () {
  },
  
  onRead: function() {
  	var liveroom = this.selectComponent("#id_liveroom");
  	this.setData({
  		roomName: '测试',
  	});
	liveroom.start();
  },

})
```


> 注意：
> - demo设置了全屏，在一些页面上有设置margin-top为标题栏高度。如果您不需要全屏，去掉`app.json`中的`"navigationStyle": "custom"`，然后在`app.js`中注释掉下面代码；同时修改每个页面的标题，去掉返回按钮。
```
this.globalData.headerHeight = headHeight;
this.globalData.statusBarHeight = statusBarHeight;
```
