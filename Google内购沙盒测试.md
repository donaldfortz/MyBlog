# 记录 Google 内购测试不显示 Test card 问题

  1. 首先Google 后台选择内部测试（internal testing），上传aab包

  2. 然后配置好支付项并发布使其有效，有效的支付项状态是 active

  3. 接着设置测试人员

  4. 然后按 [Google 官方文档](https://support.google.com/googleplay/android-developer/answer/6062777?hl=zh-Hans) 设置好 license tester

  # 记得勾选生效！

  5. 最后使用内部测试链接下载安装待测试应用，进入应用并点击支付项调起Google支付

# 异常记录：

  1. 使用测试账号下载安装应用后，无法调起test card 支付模式，而是提示绑卡，看着像是正式支付流程（测试账号并未绑卡）

     1. 清除 Google play 、Google service 应用缓存数据，退出已登陆账号，并重新登陆测试账号，无效
     2. Google 后台重新创建 tester list，重新添加邮箱；修改License response为：LICENSE，无效
     3. 重启手机，无效

#  等了1天后发现问题解决，所以是 Google play 后台添加 License tester 有生效延时，大概24h

# 参考资料
  https://stackoverflow.com/questions/64803471/payment-method-required-for-license-tester-on-google-play
  https://qichehuizhan.com/2022/08/25/Wiki/4.Others/Archive/8.Google%E6%94%AF%E4%BB%98%E6%B2%99%E7%9B%92%E6%B5%8B%E8%AF%95/
  https://blog.csdn.net/weixin_41548050/article/details/128663066
