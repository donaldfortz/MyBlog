
### 注：此流程开始前需修复升级引擎导致的所有cocos报错

### 1. 构建完成后，首先下载项目依赖的第三方库。cd 到 /build/ios/proj/ 目录，执行：

  ```
  $pod init
  ```
  ### 修改 Podfile，如下：

  ```
  target 'TongitsKingdom-mobile' do
  # Comment the next line if you don't want to use dynamic frameworks
  use_frameworks!

  # Pods for TongitsKingdom-mobile
    pod 'FBSDKLoginKit'
    pod 'FBSDKCoreKit'
    pod 'FBSDKShareKit'
    pod 'FirebaseAnalytics'
    pod 'FirebaseCrashlytics'
    pod 'FirebaseDynamicLinks'
    pod "RecaptchaEnterprise", "18.3.0"
    pod 'AppsFlyerFramework'
    pod 'FirebaseMessaging'
  end
  ```
  ### 执行：
  ```
  $pod install
  ```
### 2. 导入项目依赖的系统库

  ![image](https://github.com/Joooenn/blog/assets/15683811/0e36bc4f-89f5-40d6-800a-f8948d59225f)

## 特别注意，导入native/engine/ios目录下的文件前，先新建一个 Bridge.swift 触发OC-Swift桥接

### 3. 导入项目原生代码，点击+号后，去往项目根目录 /native/engine/ios，选中所有自定义的原生代码文件

  ![image](https://github.com/Joooenn/blog/assets/15683811/3c97edef-3598-4611-b680-dcf5cd553524)


### 4. 还原项目配置

  #### Associated Domains
  applinks:tongitskingdom.page.link

  #### Queried URL Schemes
  - fbapi
  - fb-messenger-share-api
  - fb
  - fbauth2
  - fbshareextension
  - fb-messenger-api
  - fb-messenger

  #### Privacy - Tracking Usage Description
  Your data will be used to provide you with personallized content

  ![image](https://github.com/Joooenn/blog/assets/15683811/918d16a8-ed52-4adf-91e1-b83985e14be6)

  ![image](https://github.com/Joooenn/blog/assets/15683811/0c3549cc-d07c-4c72-b948-fe407b318d3f)

  ![image](https://github.com/Joooenn/blog/assets/15683811/1efdcd68-e77f-4c3b-999a-e3bcacd9290c)

  ![image](https://github.com/Joooenn/blog/assets/15683811/8be91d8d-396a-45f5-8833-dc1ae2064601)

  ![image](https://github.com/Joooenn/blog/assets/15683811/7a2aa302-bef3-40e3-bc68-4504c2a32043)

  ![image](https://github.com/Joooenn/blog/assets/15683811/e44b02cc-27a6-46ce-9b45-8f73c8649ed3)

  ![image](https://github.com/Joooenn/blog/assets/15683811/5cfd4e91-75cc-419b-b302-749b9dd8915c)


### 5. 打包报错：

  ### 1. Vision OS支持
  ```
  /Users/stotsenberg/Desktop/Qokori-3.8.2/Qokori/native/engine/ios/Base.lproj/LaunchScreen.storyboard: Failed to find or create execution context for description '<IBCocoaTouchPlatformToolDescription: 0x600002d39220> System content for IBVisionIdiom-SevenAndLater <IBScaleFactorDeviceTypeDescription: 0x600002d38bc0> scaleFactor=2x, renderMode.identifier=(null)'.
  ```
  ### 解决方案：Xcode->Setting->Platform->安装Vision OS

  ### 2. weak 配置
  ```
  Cannot create __weak reference in file using manual reference counting
  ```
  ### 解决方案： https://stackoverflow.com/questions/36147625/xcode-7-3-cannot-create-weak-reference-in-file-using-manual-reference-counting

  ![image](https://github.com/Joooenn/blog/assets/15683811/786858cf-ce94-435b-bee1-21076eb0e147)

  ### 3. Swift 桥接问题

  ![image](https://github.com/Joooenn/blog/assets/15683811/a080106d-f335-4250-9cab-4c1e804913a3)

  ### 解决方案：

  ![image](https://github.com/Joooenn/blog/assets/15683811/2f39b468-b01d-49fc-a755-67a77377b93d)

  ### 4. pod 库找不到

  ![image](https://github.com/Joooenn/blog/assets/15683811/675f4eee-64d1-478f-97e4-e6abdbfe5d35)

  ### 解决方案：

  ![image](https://github.com/Joooenn/blog/assets/15683811/b9de7f4f-fae9-49b0-9888-9b96062280db)

  https://stackoverflow.com/questions/76012635/while-initialising-recaptcha-enterprise-in-ios-app-im-getting-unrecognized-sele

  ## Pod swift 混编的库都报 XXX file not found

  #### 对比可能与此有关
  ![image](https://github.com/Joooenn/blog/assets/15683811/1072eba8-bd99-44ab-9f41-1aed52dbdae9)

  修复方案：
  - 删除build目录下的ios文件夹，
  - 修改 Cocos build 工程名从 TongitsKingdom -> Qokori
  - 重新构建
  - 按以上步骤重新配置iOS工程
  
  ## PS：特别注意，导入native/engine/ios目录下的文件前，先新建一个 Bridge.swift 触发OC-Swift桥接

  







