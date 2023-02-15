---
layout:     post
title:      "iOS集成IJKPlayer问题"
subtitle:   " \"记录一下\""
# date:       2023-02-15 14:22:00
author:     "Ade"
header-img: "img/zujian.jpg"
catalog: true
featured-condition-size: 2
tags:
    - 技术
---

> “IJKPlayer集成”


## IJKPlayer运行demo:

* brew -v error: Version value must be a string; got a NilClass () (TypeError)

```js
    解决方案：/usr/local/Homebrew/Library/Taps/homebrew 删除 homebrew-core 文件
```
* ./compile-ffmpeg.sh all 报错： error: unknown directive
```js
    解决方法:  Xcode 弱化了对 32 位的支持, 在 compile-ffmpeg.sh 中删除 armv7 , 修改如下:
    FF_ALL_ARCHS_IOS8_SDK="arm64 i386 x86_64"
    再重新执行脚本: ./compile-ffmpeg.sh all 
```

## IJKPlayer集成项目:

* 项目Product文件夹不显示
```js
    进入IJKMediaPlayer文件夹（目录：ijkpalyer-ios/ios/IJKMediaPlayer），选中IJKMediaPlayer.xcodeproj，右键显示包内容，双击打开project.pbxproj，全局搜索mainGroup，mainGroup的值替换掉下一行productRefGroup的值包括注释。
```
* 合并frameWork 报错 have the same architectures (arm64) and can't be in the same fat output file
```js
    将模拟器的arm64给排除出去，buildSetting - Architectures - Excluded Architectures -Debug/Release  选择Any iOS Simulator SDK 添加上arm64
```


## 结束

欢迎交流。

—— Ade 


