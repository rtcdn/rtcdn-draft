# rtcdn-draft

WebRTC CDN 集成规范


## 编解码能力

WebRTC目前视频编解码支持VP8/VP9/H264,  音频默认支持OPUS。考虑到跟RTMP互通, 视频编解码码限定支持H264 Baseline, 音频编解码限定支持OPUS.


## 请求URL说明

WebRTC协议接入URL参考RTMP协议

#### 推流URL

URL: schema://domain:port/app/stream

```
schema: http或者https
app：跟rtmp协议中app对齐
stream: 跟rtmp协议中的stream对齐
```

HTTP请求：

method: POST
body: 使用json编码

```
{
  type:"publish",
  sdp:string   // offer sdp
}
```

HTTP响应：

```
{
  sdp:string   // answer sdp 
}
```


HTTP响应码：

```
200:  正常影响
400:  请求不正确，URL 或者 参数不正确
403:  鉴权失败
409:  该流已经存在  
```

####  拉流URL

URL: schema://domain:port/app/stream

```
schema: http或者https
app：跟rtmp协议中app对齐
stream: 跟rtmp协议中的stream对齐
```

HTTP请求：

method: POST
body: 使用json编码


```
{
  type:"play",
  sdp:string   // offer sdp
}
```

HTTP响应：

```
{
  sdp:string   // answer sdp 
}
```


HTTP响应码：

```
200:  正常影响
400:  请求不正确，URL 或者 参数不正确
403:  鉴权失败
404:  该流不存在  
```

## 流的保活

WebRTC的流需要保活， WebRTC默认2.5s发送一次Stun Binding Request， 服务端可以监听此Bingding equest,  在5秒内没有收到此请求可以主动将流清理， 完成资源的释放。

