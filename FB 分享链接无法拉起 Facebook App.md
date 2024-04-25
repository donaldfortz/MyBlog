# 史前巨坑！！！
# Android 调 FB-SDK 分享，无法拉起 Facebook App！

# 表现上看着像马上要拉起来了，但又终止了：

![image](https://github.com/liangzcn/blog/assets/15683811/e1f553eb-ab9e-49be-bc78-11f6f56b2d82)

通过图片可以看出屏幕上下两端的导航条都出来了，正常情况紧接着就会跳到 FacebookApp 的分享页面。

## 于是各种翻文档，网上查资料，中英文查了个遍，确认FB-SDK接入没问题，于是查 ChatGPT：

# 在 Android 应用中调用 Facebook 分享链接的方法时，如果无法调起 Facebook App，可能有以下一些原因：

#### 1. Facebook App 未安装或版本过低：
用户设备上没有安装 Facebook App，或者安装的版本过低，不支持当前使用的分享方法。

## 2. 缺少必要的权限或配置：
确保你的应用已经正确配置了 Facebook 开发者平台上的应用设置，并且拥有正确的权限。

## 3. 未正确配置 Facebook 应用的 Android Key Hash：
在 Facebook 开发者平台配置应用时，需要添加正确的 Android Key Hash。如果 Key Hash 不匹配，Facebook App 将无法正确处理分享请求。

#### 4. Facebook App 不支持当前的分享内容或链接：
确保你要分享的内容符合 Facebook App 的支持标准。有时候，特定的内容或链接可能不被 Facebook App 支持。

#### 5. 深度链接配置问题：
如果你在分享链接中使用了深度链接，确保配置正确。深度链接通常用于在用户点击链接时导航到应用内的特定页面。

#### 6. Facebook App 的 Bug 或更新问题：
有时 Facebook App 可能存在 Bug，或者用户的应用版本较老。确保你的应用使用的是最新版本的 Facebook SDK，并且考虑处理一些 Facebook App 版本的兼容性问题。

#### 7. 系统或设备的问题：
一些设备或系统可能存在兼容性问题，导致 Facebook App 无法正确处理分享请求。这可能是设备或系统级别的问题，难以直接解决。

#### 8. Facebook SDK 集成问题：


# 由于调SDK分享的时候没有任何报错，所以只能一个个排除异常原因，并重点关注2、3两点。
## 查 [FB官方文档](https://developers.facebook.com/docs/android/getting-started?locale=zh_CN#quick-start) 可以得到 keyhash 的创建方式：
### 1. 命令行 
```
keytool -exportcert -alias key0 -keystore D:\project\wsgtl\Qokori\script\build_keys\TG.jks | C:\Users\Administrator\Downloads\openssl-0.9.8k_X64\bin\openssl sha1 -binary | C:\Users\Administrator\Downloads\openssl-0.9.8k_X64\bin\openssl base64
```
### 2. 代码
```
// 经过验证此方式获取的 keyhash 才是正确的
// 通过命令行创建的 keyhash 可能是错的，原因未知
// 将此代码放到项目主 Activity 的 onCreate 里
try {
    PackageInfo info = getPackageManager().getPackageInfo(
                    "com.tongitskingdom.pokerplus",
                    PackageManager.GET_SIGNATURES);
    for (Signature signature : info.signatures) {
        MessageDigest md = MessageDigest.getInstance("SHA");
        md.update(signature.toByteArray());
        Log.i("KeyHash:", Base64.encodeToString(md.digest(), Base64.DEFAULT));
    }
} catch (PackageManager.NameNotFoundException e) {
    Log.i("KeyHash e1:", "error");
} catch (NoSuchAlgorithmException e) {
    Log.i("KeyHash e2:", "error");
}
```
获取正确的 keyhash 后（ps: 这里其实是通过调 FB 登录，拉起 FB 登录页面，通过该页面的错误提示得知 keyhash 不对），继续测试分享...
结果还是不对，偶然间翻FB后台，看到有测试用户选项，恍然大悟：**开发模式下是不是测试用户才能使用登录、分享这些功能？！**

![image](https://github.com/liangzcn/blog/assets/15683811/18e7c083-4444-47a7-9e35-006642f54e23)

遂将一直使用的FB账号加入成为测试用户，All Right！问题迎刃而解！


# 总结 Android 调 FB-SDK 分享拉不起 App 的可能原因：
1. 网络异常，未翻墙，检测一下VPN；
2. keyhash 配置不对；
3. FB后台应用开发模式处于开发中，但未使用FB测试用户进行测试；

