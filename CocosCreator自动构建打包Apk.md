## Jenkins 笔记：

### 安装
![image](https://github.com/Joooenn/blog/assets/15683811/80890ac3-0410-47a6-999e-a40eb53278f6)

### 运行
```
 # java -jar jenkins.war --httpPort=8080
```
![image](https://github.com/Joooenn/blog/assets/15683811/ac8c48ef-e076-41de-a845-ba1c4f7d6c8f)


### Cocos Jenkins 构建
[官方文档](https://docs.cocos.com/creator/manual/zh/editor/publish/publish-in-command-line.html)


### 问题记录

1. 构建异常，找不到依赖库，可以在 Jenkins 中执行 npm install（PS：也可以命令行进到 C:\Users\Administrator\.jenkins\workspace\Your-Project 路径下，执行 npm i）
   
   ![image](https://github.com/liangzcn/blog/assets/15683811/583393bb-ffad-4506-a011-192a27657e9f)

3. 完成以上步骤后build -> 构建成功但会退出命令执行exit 36，且构建失败，（PS：Jenkins 只认 exit 0 为成功，所以这里构建失败）

   ![image](https://github.com/liangzcn/blog/assets/15683811/08ed7291-fb58-46e0-a496-32ee1856beb9)

   经过论坛大佬提醒，这里需要将Cocos Creator 的构建命令封装，捕获到 exit 36 不让它退出， Jenkins 封装 CocosCreator Windows 构建命令行步骤如下：
   
   a. 首先在项目中创建一个build_cocos.js文件，并使用 node:child_progress 进行封装，代码如下：
   ```
    // build_cocos.js
    const { spawn } = require("node:child_process");

    function runCocosCreatorBuild() {
        /** 构建配置 */
        const cocosCreatorPath =
            "C:\\ProgramData\\cocos\\editors\\Creator\\3.7.3\\CocosCreator.exe";
        const buildConfigPath = "./script/build_cmd/buildConfig_android.json";

        /** Cocos Creator Windows 构建命令 */
        const command = `${cocosCreatorPath} --project ./ --build "configPath=${buildConfigPath}"`;
        /** 使用 spawn 生成子进程执行命令 */
        const process = spawn(command, { shell: true });
        /**
         * 监听 stdout 和 stderr 事件
         * 因为 spawn 生成子进程，流式且无回调
         * 需要这两个事件来输出日志
         */
        process.stdout.on("data", (data) => {
            console.log(`CocosCreator stdout: ${data}`);
        });

        process.stderr.on("data", (data) => {
            console.error(`CocosCreator stderr: ${data}`);
        });

        /** 监听 exit 事件 */
        process.on("exit", (code) => {
            console.log(`CocosCreator process exited with code ${code}`);
            if (code === 36) {
            console.log("Exit code is 36. Returning...");
            return; // 因为还有执行后续打包流程，这里不能 exit，固 return
            } else if (code === 34) {
            console.log("构建工程 -构建失败（构建过程出错失败，详情请参考构建日志）");
            } else if (code === 32) {
            console.log("构建工程 -构建失败（构建参数不合法）");
            }
            process.exit(1);
        });

        /** 监听 error 事件 */
        process.on("error", (err) => {
            console.error("Error executing CocosCreator:", err);
            process.exit(1);
        });
    }
    /** 开始打包 */
    runCocosCreatorBuild();
   ```
   b. 在Jenkins中增加配置项

   ![image](https://github.com/liangzcn/blog/assets/15683811/8966b130-9048-4809-8337-0869a55f9b89)

5. 根据项目业务需要，细化构建步骤
   
   ![image](https://github.com/liangzcn/blog/assets/15683811/30363042-174e-4a09-a0ed-5b977ae48655)

6. 完成以上继续 build -> Freestyle project 构建失败并提示 FATAL: The Gradle wrapper has not been found
   
   答：路径填错了导致Jenkins没办法找到相应文件，修改配置继续

   ![image](https://github.com/liangzcn/blog/assets/15683811/52d168ce-8f0b-42ff-9135-b64e98bb0146)

7. 完成以上步骤 build -> downloading gradle 卡住，导致构建超时

   答：去到 Dashboard -> Manager Jenkins -> Plugins -> Advanced Settings 设置全局代理

8. 设置好代理继续 build -> 构建终止，抛出异常：Task 'clean' not found in root project 'tongits-preMaster'.

   答：增加项目根目录配置

   ![image](https://github.com/liangzcn/blog/assets/15683811/52377ebe-6358-4b29-b3be-63f51069bbc7)

9. 继续 build -> 构建终止，抛出异常：Could not open settings generic class cache for settings file 'C:\Users\Administrator\.jenkins\workspace\tongits-preMaster\build\android\proj\settings.gradle'

    ![image](https://github.com/liangzcn/blog/assets/15683811/3206d496-88dc-414e-8643-5d1e2b6826ed)

   答：尝试去到 C:\Users\Administrator\.gradle 目录下删除caches文件夹，未解决;
   尝试用 Android studio 打开项目（proj），同步Gradle，未解决；且异常改变，变成构建报错卡住只能等超时退出，此问题通过删除项目根目录下的library、temp文件夹解决；

   继续网上查资料，[发现是jdk版本太高，安装jdk8可解](https://stackoverflow.com/questions/67240279/could-not-open-settings-generic-class-cache-for-settings-file)

   这里需要吧 jdk-8 设置为 JAVA_HOME，否则 Jenkins 那边无法识别，环境变量设置完成后再到 Jenkins dashboard -> Jenkins Manager -> Tools 设置 JDK8

   ![image](https://github.com/liangzcn/blog/assets/15683811/9766aaf0-5daf-4a4b-922b-bc32ce5fa3c9)

   完成以上步骤重新 build -> 异常变更为：

   ![image](https://github.com/liangzcn/blog/assets/15683811/51cd2abe-e0ba-4776-8b56-baa6cd50744c)

   这是由于此第三方库需要在 build.gradle 中修改Maven 仓库指向，否则找不到，改动如下：

   ![image](https://github.com/liangzcn/blog/assets/15683811/4184ffae-89fc-4d9b-b7c4-0662009e5ca5)

   完成以上步骤重新 build -> 异常变更为：

   ![image](https://github.com/liangzcn/blog/assets/15683811/4505a4e0-0064-46d5-a7ba-e0ce1e0b7ba5)

   去到JDK目录下确实找不到 tools.jar 这个文件，[查 StackOverflow 发现可能是JDK的问题](https://stackoverflow.com/questions/19722058/where-to-get-the-tools-jar-to-use-with-the-java-8-jdk-early-release)，换一个JDK8

   ![image](https://github.com/liangzcn/blog/assets/15683811/e38420af-5589-4e51-997f-d4d57aa36c48)

   重新build -> 异常变更为：

   ![image](https://github.com/liangzcn/blog/assets/15683811/83a1695b-aaae-450f-b095-aac83cff51ea)

   找不到签名文件导致的，去到Android打包配置文件 buildConfig_android.json，修改配置：

   ![image](https://github.com/liangzcn/blog/assets/15683811/fd8d2a54-73ce-4df4-a8b9-2f0b8e4732bd)

   重新build -> 异常不变！调试发现每当开始构建时，buildConfig_android.json 都会回滚为初始版本的内容
   排查发现可能是每次构建前会执行 git pull，而将远程仓库的内容覆盖掉了本地的修改，于是将本地修改同步到远程仓库

   重新build -> 异常变更：

   ![image](https://github.com/liangzcn/blog/assets/15683811/f9ba9c34-e859-4499-b1bb-231f822c1006)

   网络问题，查资料配置 Powershell 代理:
   ```
   $env:HTTP_PROXY="http://127.0.0.1:7890"
   $env:HTTPS_PROXY="http://127.0.0.1:7890"
   ```
   貌似没效果，在命令行执行 ping firebasecrashlyticssymbols.googleapis.com 会超时
   改为使用 Clash 的 TUN Model：

   ![image](https://github.com/liangzcn/blog/assets/15683811/7fba4eca-193c-4f69-933b-610cbe4ee398)

   能 ping 通了！

   ![image](https://github.com/liangzcn/blog/assets/15683811/1ef5a153-b5fb-4f86-9276-6c367c2d1aaf)


   重新build -> 异常变更：

   ![image](https://github.com/liangzcn/blog/assets/15683811/a8f7675a-2111-423f-a7e8-8e3f4aa213ea)

   这次是签名文件出问题，用Android Studio 打开 workspace 构建好的项目，手动build发现签名配置是空的：

   ![image](https://github.com/liangzcn/blog/assets/15683811/2f5b3870-91e9-42b3-a862-b194cb4ee79c)

   按项目配置好后，手动打包试试，OK构建成功！

   ![image](https://github.com/liangzcn/blog/assets/15683811/49f9ecfa-90fb-4426-b8a9-b2f9b9f60b31)

   继续尝试 Jenkins 构建 -> 异常没变

   经过分析发现是 jdk 版本原因
   ```
   // 验证密钥是否有效
   keytool -list -v -keystore D:\CocosProjects-Work\Qokori-skin\Qokori\script\build_keys\TG.jks
   ```

   ![image](https://github.com/liangzcn/blog/assets/15683811/ccb077c4-1e3f-46f7-bf10-0e8334eafdd8)

   ![image](https://github.com/liangzcn/blog/assets/15683811/a3f6464c-bf58-4f76-9889-ecc624f5ab33)

   修改Jenkins JDK配置：

   ![image](https://github.com/liangzcn/blog/assets/15683811/2d9111eb-1779-4938-9c16-e63d5a9089c0)

   完成配置，继续build -> 异常变更：

   ![image](https://github.com/liangzcn/blog/assets/15683811/3efffab1-8660-4bdb-b9e1-e36965b41b5b)

   分析得出结论，JDK与.jks密钥文件应保持一致，否则会报错：Invalid keystore format
   怎么保持一致呢？因为项目需要，打包时的JDK版本为JDK-8，那么生成.jks的keytool版本也应对应JDK-8，我们通过命令行重新创建.jks：
   
   ```
   keytool -genkeypair -v -keystore C:\Users\Administrator\.jenkins\workspace\tongits-preMaster\script\build_keys\TG.jks -keyalg RSA -keysize 2048 -validity 9125 -alias key0
   ```

   ![image](https://github.com/liangzcn/blog/assets/15683811/48ce4cbd-4e15-4e5b-bbf9-e85b73e23721)

   # PS: 如果配置了git远程仓库，一定要把新创建的.jks文件提交到远程仓库，不然你就会像我一样懵逼，然后花大半天的时间找 Invalid keystore format 的原因

   完成以上步骤，继续build -> 终于构建成功!
   
   ![image](https://github.com/liangzcn/blog/assets/15683811/762a3283-7823-40e7-8378-e1d71ac4f216)

   ![image](https://github.com/liangzcn/blog/assets/15683811/e00b98d5-33ef-497d-a51a-4bb7de6de117)


# 发送构建消息到企业微信
   1. 添加群机器人，获取Webhook地址：
      ```
      https://qyapi.weixin.qq.com/cgi-bin/webhook/send?key=23cc3c6f-c2d2-41fa-89a7-75cd20df6d52
      ```
   2. 完善配置

      ![image](https://github.com/liangzcn/blog/assets/15683811/1e5c491a-8ed3-484c-bac3-c527e6529de5)

   4. 大功告成！

      ![image](https://github.com/liangzcn/blog/assets/15683811/27b28dbc-15ff-4e45-ab1c-b4e7530459e4)


      


这位作者大大，图片都看不到了，能否修正下？
















   
   

   
   
