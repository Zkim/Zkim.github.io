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

## IJKPlayerDemo:
```js
     let options = IJKFFOptions.byDefault()
        
        // 缩短播放的rtmp视频延迟在1s内
        options?.setOptionIntValue(100, forKey: "analyzemaxduration", of: kIJKFFOptionCategoryFormat)
        options?.setOptionIntValue(10240, forKey: "probesize", of: kIJKFFOptionCategoryFormat)
        options?.setOptionIntValue(1, forKey: "flush_packets", of: kIJKFFOptionCategoryFormat)
        options?.setOptionIntValue(1, forKey: "infbuf", of: kIJKFFOptionCategoryFormat)
        options?.setOptionValue("nobuffer", forKey: "fflags", of: kIJKFFOptionCategoryFormat)
        options?.setOptionValue("allow_media_types", forKey: "video", of: kIJKFFOptionCategoryFormat)


        options?.setOptionIntValue(0, forKey: "videotoolbox", of: kIJKFFOptionCategoryPlayer)
        //设置播放器缓冲: 0-关闭缓冲；1-开启缓冲；默认是1
        options?.setOptionIntValue(0, forKey: "packet-buffering", of: kIJKFFOptionCategoryPlayer)
//        options?.setOptionIntValue(1, forKey: "framedrop", of: kIJKFFOptionCategoryPlayer)

        //设置解码方式: 0-软解码；1-硬解码
        options?.setPlayerOptionIntValue(0, forKey: "videotoolbox")
        // 最大缓存是3000（3s），可以依据自己的需求修改
        options?.setPlayerOptionIntValue(1000, forKey: "max_cached_duration")
                //设置自动转屏
        //        options?.setFormatOptionIntValue(0, forKey: "auto_convert")
        //设置重连：0-关闭重连；1-开启重连；
        options?.setFormatOptionIntValue(1, forKey: "reconnect")
        //如果使用rtsp协议，可以优先用tcp（默认udp）
        options?.setPlayerOptionValue("tcp", forKey: "rtsp_transport")
                //开启环路滤波IJK_AVDISCARD（0比48清楚，但解码开销大，48基本没有开启环路滤波，清晰度低，解码开销小）
//                options?.setCodecOptionIntValue(48, forKey: "skip_loop_filter")
                //帧速率(fps)-可以改，确认非标准桢率会导致音画不同步，所以只能设定为15或者29.97
        //        options?.setPlayerOptionIntValue(29.97, forKey: "r")

        //        //设置音量大小，256为标准音量。要设置成两倍音量时则输入512，依此类推
        //        options?.setPlayerOptionIntValue(256, forKey: "vol")
                //设置静音
        options?.setPlayerOptionValue("1", forKey: "an")
        IJKFFMoviePlayerController.setLogLevel(IJKLogLevel(rawValue: 3))
        IJKFFMoviePlayerController.setLogReport(true)

                 
        //视频源地址
        let url = URL(string: "rtmp://ns8.indexforce.com/home/mystream")
        //"rtmp://ns8.indexforce.com/home/mystream"
        //初始化播放器，播放在线视频或直播（RTMP）
        let player = IJKFFMoviePlayerController(contentURL: url, with: options)

        player?.view.frame = CGRect(x: 0, y: 100, width: kScreenWidth, height: 400)
        
        self.view.autoresizesSubviews = true
        self.view.addSubview((player?.view) ?? UIView())
        
        let btn = UIButton(frame: CGRect(x: 0, y: 600, width: 50, height: 50))
        btn.backgroundColor = .orange
        btn.addTarget(self, action: #selector(play), for: .touchUpInside)
        self.view.addSubview(btn)
        
        self.player = player

    @objc func play() {
        DispatchQueue.main.async {
            //开启播放器
            self.player.prepareToPlay()
            self.player.play()
        }
    }

    override func viewWillDisappear(_ animated: Bool) {
        super.viewWillDisappear(animated)
        player.pause()
    }
```
## 结束

欢迎交流。

—— Ade 


