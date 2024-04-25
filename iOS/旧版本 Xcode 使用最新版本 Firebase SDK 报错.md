### 背景
    1. 公司的破 Mac 15年产的，Mac OS 只支持到12，导致更新不了 Xcode 14；
    2. 公司项目 pod 管理的第三方库没加版本号限制:
    
```
target 'Qokori-mobile' do
  # Comment the next line if you don't want to use dynamic frameworks
  use_frameworks!

  # Pods for Qokori-mobile
  pod 'FBSDKLoginKit'
  pod 'FBSDKCoreKit'
  pod 'FBSDKShareKit'
  pod 'FirebaseAnalytics'
  pod 'FirebaseCrashlytics'
  pod 'FirebaseDynamicLinks'
  pod 'RecaptchaEnterprise', '18.1.2'
  pod 'AppsFlyerFramework'
  pod 'FirebaseMessaging'
end
```

### 起因
    某次 pod 需要新增一个第三方SDK，结果不知道是VPN 罢工还是 pod 服务器抽风，pod 半天拉不下，
    结果不出意外的出了意外
    鬼使神差的敲了 pod update
    结果第三方 SDK 的版本号统统更到最新版本
    然后 Xcode 就编译不过了

  <img width="1915" alt="截屏2023-08-03 13 49 44" src="https://github.com/liangzcn/blog/assets/15683811/1a00baee-4203-44e4-9335-aeecf220b34e">

    报错很多，足足100个，
    初看确实很吓人，但冷静下来，
    报的错全都是 "undefined symbols"，也就是找不到定义
    合理推论是因为更新 SDK 后，新版本的 SDK 不支持旧的 Xcode，遂 Google 搜索 firebase release notes 查到一下内容：

  ![image](https://github.com/liangzcn/blog/assets/15683811/74a7ffa4-e225-4efb-832d-c71e1040dd1c)
  ![image](https://github.com/liangzcn/blog/assets/15683811/79573956-1a76-4327-a667-dc3d3f508a81)

    可见 firebase SDK 对 Xcode 最低版本是有要求的，于是选择 pod SDK 降级:

```
target 'Qokori-mobile' do
  # Comment the next line if you don't want to use dynamic frameworks
  use_frameworks!

  # Pods for Qokori-mobile
  pod 'FBSDKLoginKit'
  pod 'FBSDKCoreKit'
  pod 'FBSDKShareKit'
  pod 'FirebaseAnalytics', '10.0.0'
  pod 'FirebaseCrashlytics', '10.0.0'
  pod 'FirebaseDynamicLinks', '10.0.0'
  pod 'RecaptchaEnterprise', '18.1.2'
  pod 'AppsFlyerFramework'
  pod 'FirebaseMessaging', '10.0.0'
end
```
    pod update 
    every thing all right!

### 注：[firebase release notes](https://firebase.google.com/support/release-notes/ios)

