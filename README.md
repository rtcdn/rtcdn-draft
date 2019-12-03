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
  sessionid:string,  // 唯一id，用来定位流查询问题 
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
  sessionid:string,  // 唯一id，用来定位流查询问题 
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


## 鉴权

URL: schema://domain:port/app/stream?query=xxxxxx

推拉流的URL应当支持query参数， WebRTC-CDN中可以根据响应的query参数来做鉴权


## 与RTMP的互通

在设计的WebRTC的时候要考虑到对接原有的RTMP系统， WebRTC-CDN URL Schema 设计为与RTMP一致。 如果想要WebRTC系统和RTMP系统进行互通，CDN需要做音频的转码重采样和协议的转封装。
在上行的WebRTC下行RTMP的时候，CDN需要把opus编码的音频转码为aac, WebRTC的的opus默认为48k采样率，RTMP中的AAC大多是44.1k采样率，这里需要重采样。相反RTMP协议转封装为WebRTC的过程中，
需要把aac转码为opus， 并做重采样。


## 流的保活

WebRTC的流需要保活， WebRTC默认2.5s发送一次Stun Binding Request， 服务端可以监听此Bingding equest,  在5秒内没有收到此请求可以主动将流清理， 完成资源的释放。


 