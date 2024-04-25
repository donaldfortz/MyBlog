### login error 304 facebook/facebook-ios-sdk#713
解决方案：
```
初始化 FBSDKLoginManager后，添加如下代码:
[login logOut];
```

### login error 309 facebook/facebook-ios-sdk#1879

解决方案:
推测是SDK问题，尝试更新SDK

PS: 同一个APP包，貌似部分机型无法解决309的问题，
排除了账号原因，SDK与FBAPP版本都保持一致的情况下，推测可能跟iOS系统版本有关
