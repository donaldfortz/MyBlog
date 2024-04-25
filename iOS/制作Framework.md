# 正常流程

#### 1. Xcode新建项目 - Frame & Library -> Framework

#### 2. 实现SDK代码逻辑

#### 3. 配置framework：

  1. Build Settings配置：
     
    Product Name ：可以修改名称，这里的名称是最终打包出来的framework的名称，博主这里叫PrintFramework。
    Mac-O Type：选择Static Library。
    Build Active Architeture Only：设置为NO。
    Base SDK：选择iOS。
    iOS Deployment Target：选择打包出来的framework最低支持的iOS版本。

  2. Build Phases配置：
     
    Copy Bundle Resource：在这里面添加 framework项目使用到的一些资源文件，包括：xib、plist、图片等。
    Link Binary With Libraries：在这里面添加 framework项目的依赖库。
    Compile Sources ：framework项目包含的实现文件。
    Headers：将要暴露出来的头文件放在public 下，不需要暴露在外面的头文件放在project下。

#### 4. Edit Scheme - run模式改为release模式。
#### 5. 分别在真机和模拟器下 Commond+B 编译framework项目
#### 6. 合并模拟器版本与真机版本，使framework能自如的在模拟器和真机运行
#### 7. 使用注意：如果导入的是 dynamic-framework，需要打开General -> Frameworks, Libraries, and Embedded Content -> 将 FPMS.xcframework 改为 Embed & Sign

# 避坑，这里步骤6使用 lipo -create 命令虽然能合并两个模式下的 framework，但使用时会抛出异常，无法通过编译：
```
Building for iOS Simulator, but the linked and embedded framework 'xxx.framework' was built for iOS + iOS Simulator [duplicate]
```
#### 这是因为Xcode11后，Apple在设计上加强了限制：“同一个framework中不应包含模拟器、真机两种架构”，并要求开发者使用xcframework来实现模拟器-真机兼容的framework
#### 这里备份一个合并模拟器——真机两个架构framework的Aggregate脚本：
```
#!/bin/sh

#  XCFramework.sh
#  FPMS
#
#  Created by sinonet on 2024/1/11.
#
TARGET_NAME=${PROJECT_NAME}
if [[ $1 ]]
then
TARGET_NAME=$1
fi
UNIVERSAL_OUTPUT_FOLDER="${SRCROOT}/Products"

#创建输出目录，并删除之前的 framework 文件
mkdir -p "${UNIVERSAL_OUTPUT_FOLDER}"
rm -rf "${UNIVERSAL_OUTPUT_FOLDER}/${TARGET_NAME}.framework"

#分别编译模拟器和真机的 framework
xcodebuild -target "${TARGET_NAME}" ONLY_ACTIVE_ARCH=NO -configuration ${CONFIGURATION} -sdk iphoneos BUILD_DIR="${BUILD_DIR}" BUILD_ROOT="${BUILD_ROOT}" clean build
xcodebuild -target "${TARGET_NAME}" ONLY_ACTIVE_ARCH=NO -configuration ${CONFIGURATION} -sdk iphonesimulator BUILD_DIR="${BUILD_DIR}" BUILD_ROOT="${BUILD_ROOT}" clean build

#拷贝 framework 到 univer 目录
cp -R "${BUILD_DIR}/${CONFIGURATION}-iphonesimulator/${TARGET_NAME}.framework" "${UNIVERSAL_OUTPUT_FOLDER}"

#合并 framework，输出最终的 framework 到 build 目录
xcodebuild -create-xcframework -framework "${BUILD_DIR}/${CONFIGURATION}-iphonesimulator/${TARGET_NAME}.framework" -framework "${BUILD_DIR}/${CONFIGURATION}-iphoneos/${TARGET_NAME}.framework" -output "${UNIVERSAL_OUTPUT_FOLDER}/${TARGET_NAME}.xcframework"

#删除编译之后生成的无关的配置文件
dir_path="${UNIVERSAL_OUTPUT_FOLDER}/${TARGET_NAME}.framework/"
for file in ls $dir_path
do
if [[ ${file} =~ ".xcconfig" ]]
then
rm -f "${dir_path}/${file}"
fi
done

#判断 build 文件夹是否存在，存在则删除
if [ -d "${SRCROOT}/build" ]
then
rm -rf "${SRCROOT}/build"
fi
rm -rf "${BUILD_DIR}/${CONFIGURATION}-iphonesimulator" "${BUILD_DIR}/${CONFIGURATION}-iphoneos"

#打开合并后的文件夹
open "${UNIVERSAL_OUTPUT_FOLDER}"

```

# 以上流程里，第6步合并模拟器与真机库有两种操作方式，1为上述命令行方式，下面讲讲第2种自动化脚本方式：

## 1. 首先, 创建Target，File->New->Target->Other->Aggregate

## 2. 在项目根目录创建脚本文件，XCFramework.sh，内容上文放出来了

## 3. 添加脚本，Aggregate名->Build Phase->左上+->New Run Script Phase->
  ```
  ${SRCROOT}/XCFramework.sh
  ```
## 4. 编译，分别使用 Commond+B 编译模拟器与真机

## 5. 切换至 Aggregate target，选Any iOS Device (arm64)，Commond+B 编译即可生成 XCFramework 了

# 参考资料

https://codersunny.com/posts/38437bd9/

https://codersunny.com/posts/aff2362d/

https://blog.csdn.net/dingqk/article/details/124535948
  
