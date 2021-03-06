## 媒体直播相关

### 直播工作流程

| 架构端       | 工作流程                           |
| ------------ | ---------------------------------- |
| 主播端       | 音视频采集、音视频编码、推流       |
| 流媒体服务端 | 直播流收集、音视频转码、直播流分发 |
| 观众端       | 拉流、音视频解码、音视频播放       |

### 端上推拉流测试方法

| 测试方法   | 推流端                                                       | 拉流端                                                       |
| ---------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 调参数测试 | API测试：重点关注在外层比较难设置的API参数                   | **API测试**：重点关注在外层比较难设置的API参数；**协议拉流测试**：关注不同协议的播放情况；**软硬解及切换测试**：…… |
| 集成测试   | **QoS**：当带宽受限的时候（表现为掉包或延时等），自动降低编码码率，使得观众可以观看更流畅；当带宽变得比较好时又可以升高编码码率，使用户可以观看更清晰的画面。**软硬编码测试**：…… | **QoS**：在拉流SDK中可以理解成一个网络流畅度测试，包含的功能有秒开、音画同步、画质。**播放器控制功能测试**：……**播放器动态参数测试**：…… |
| 专项测试   | 美颜滤镜测试，画质评估测试，中断测试，性能测试，兼容测试，打点上报测试 | 性能测试，兼容性测试，中断测试，分辨率测试，打点上报测试     |

### 导致视频直播延时的原因

- 视频的编码格式导致。帧间压缩的视频编码，只能在收到关键帧 I 帧时候才能开始播放；
- 网络协议切片大小决定了首发数据的间隔。HLS 数据传播方式是传播 TS 文件，可能回直接导致 20～30s 的延时；
- 链路过长导致。服务器和客户端都会预留媒体缓存；

### 音视频媒体三个概念

**帧率**：1s 内包含多少数据， 影响画面流畅度。FPS

**码率**：编码器每秒编出来的数据大小。kbps，Mbps，MBps。**码率**决定了视频文件的总大小，而编码是为了在这个大小之下保存最好的图像。

**分辨率**：单位英寸上锁包含的像素点数。1080p，1080i

如果一个媒体流的码率降低了，相对静态的视频可能还是相对清晰，但是通过增加大量的运动粒子，编码器就难以预测像素点的运动，而码率又不足以支撑画面。此时画面就会出现卡顿，大量滥竽充数的色块和马赛克。

### 媒体编码

音频：AAC，opus

视频：H.264

H.264 会使用 I 帧（关键帧）、P 帧（向前参考帧）和 B 帧（双向参考帧）将视频数据压缩：

>  I --> B <--> B <-- P I

图像组（Group of pictures，GOP）指定了视频帧的组织方式。移动端直播的 GOP 都会被设置成 1 ~ 4 秒，当然我们也可以使用更长的 GOP 降低占用的带宽。





