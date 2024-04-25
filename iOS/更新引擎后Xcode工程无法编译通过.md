记录 Cocos 项目维护过程中更新引擎导致的问题
契机，由于cocos 3.6.3存在一个底层问题，会导致app在强杀时crash，进而影响后台崩溃率，[论坛](https://forum.cocos.org/t/topic/143275)得知此问题已在3.7.3版本修复，遂准备更新引擎
更新步骤：
  1. 关闭项目
  2. 下载3.7.3
  3. 删除项目中 local library temp 三个文件
  4. 使用新版引擎启动项目
  
此步骤构建 Android 工程暂未发现异常，但iOS工程出了问题：
  1. 首先，proj 目录中 cmake文件相关配置未更新，主要为旧版引擎版本号
  2. 其次，__weak 相关工程配置需重新设置
  3. 由于项目为oc-swift 混编 , swift 语言版本号也需要设置
  4. 最最重要的一点，swift 桥接会因为更新引擎失效，需要将项目中全部 swift 文件删除引用(可能无法触发创建桥接文件弹窗，试试删除所有桥接相关文件，并删除build-setting->bridge相关配置)，再重新导入，触发创建桥接文件步骤，工程才能恢复正常。

     ![image](https://github.com/Joooenn/blog/assets/15683811/ee8bfb54-ea0e-443c-91fd-41297c912a25)


### 一定要备份 info.plist！！！！！！！！！！！
### 一定要备份 Run Script、Signing & Capabilities、 General 等相关配置（截图）
