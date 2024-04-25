# iOS 突然无法连接WebSocket，且相同代码（Cocos WebSocket）情况下Android与H5可以连接
### 报错如下
```
2024-01-24 15:54:25.837668+0800 Qokori-mobile[56321:2899545] [connection] nw_socket_handle_socket_event [C18:1] Socket SO_ERROR [54: Connection reset by peer]
2024-01-24 15:54:25.837746+0800 Qokori-mobile[56321:2899028] :( Websocket Failed With Error Error Domain=SRWebSocketErrorDomain Code=2132 "Received bad response code from server: 403." UserInfo={NSLocalizedDescription=Received bad response code from server: 403., HTTPResponseStatusCode=403}
15:54:25 [DEBUG]: JS: %c[WsConnection] ["onWsError",{"type":"error","target":{"url":"wss://casualgame-api.huha555.com/userevents","URL":"wss://casualgame-api.huha555.com/userevents","protocol":""}}]
15:54:25 [DEBUG]: JS: %c[WsConnection] ["onWsClose",{"type":"close","target":{"url":"wss://casualgame-api.huha555.com/userevents","URL":"wss://casualgame-api.huha555.com/userevents","protocol":""},"code":0,"reason":"onerror","wasClean":tru
15:54:25 [DEBUG]: JS: %c[WsConnection] e},0]
2024-01-24 15:54:25.860165+0800 Qokori-mobile[56321:2899028] WebSocket (0x28140b560) was closed, no need to close it again!
```

查明原因后发现是服务端加了防火墙，而iOS端被防火墙拦住请求了
