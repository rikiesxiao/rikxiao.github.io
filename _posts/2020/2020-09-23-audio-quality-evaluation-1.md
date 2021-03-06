---
layout: post
title: "音频质量评估-1"
subtitle: '音频质量测试和评估'
author: "叉叉敌"
header-style: text
tags:
  - 音频
  - 评估
  - PESQ
  - 测试
  - 原理
  - webrtc
---

# 原理
### 音频的原理

把模拟信号转变成 数字信号，`前者是连续的，后者离散的`。因此有DAC和ADC。
需要关注的三个指标，这三个指标决定音频的质量
1. 比特率：表示经过编码（压缩）后的`音频数据每秒钟`需要用多少个比特来表示，单位常为kbps。 `这个数字越大音频质量越好，但是数据文件就越大。`

1. 响度和强度：声音的主观属性响度表示的是一个声音听来有多响的程度。响度主要随声音的强度而变化，但也受频率的影响。总的说，中频纯音听来比低频和高频纯音响一些。这个就是`振幅`。计算方式20lg(amp1/基准振幅)，基准振幅就是在声音最大的时候采用的振幅。

1. 采样和采样率：采样是把连续的时间信号，变成离散的数字信号。采样率是指`每秒钟采集多少个样本`。采样率越高其质量越好，一般在底层是48k采样率，宽带电话是16k，窄带是8k。


> Nyquist采样定律：采样率大于或等于连续信号最高频率分量的2倍时，采样信号可以用来完美重构原始连续信号。

这句话的意思大概就是， 比如采样率是16Khz, 那么频率只能达到8kHz；如果是48kHz的采样率，那么频率可以达到24kHz.

开源软件 `audacity` 的插件就是ny结尾的，里面用lisp语言写的，用于计算一些rms、THD等

- 编码方式
主流的有2中，PCM和DPCM， 
1. 脉冲编码调制 (Pulse Code Modulation)是一种对`模拟信号数字化的取样技术`，将模拟语音信号变换为数字信号的编码方式，`把时间上连续的信号变成时间上离散的信号`。把连续的编码成离散的，这里有损失，但是人耳听不出来。
2. 差分脉冲编码调制(Differential Pulse code modulation，DPCM)，是一种对模拟信号的编码模式,与PCM不同每个抽样值不是独立的编码,而是先根据前一个抽样值计算出一个预测值,再取当前抽样值和预测值之差作编码用.此差值称为预测误差.抽样值和预测值非常接近(因为相关性强),预测误差的可能取值范围比抽样值变化范围小.所以可用少几位编码比特来对预测误差编码,从而降低其比特率.这是利用减小冗余度的办法,降低了编码比特率。这个数据是网上找的，总的一句话就是`与相等的PCM比较，这种编码只需要25%的比特数`，比特率小，文件就小。

### 视频编码

1. 视频通过其中`每一帧的图像`表达信息；视频包含的音频可提供大量信息；视频通过图像的运动、场景的变换提供信息；
2. 视频信号有2种

- RGB颜色空间

- YUV颜色空间 （Y=明亮度， UV=就是色度,包含了色调和饱和度）
`没有UV信息一样可以显示完整的图像`，只不过是黑白的，这样的设计很好地解决了彩色电视机与黑白电视的兼容问题。并且，YUV不像RGB那样要求三个独立的视频信号同时传输，所以用`YUV方式传送占用极少的频宽`。

`因此 YUV编码更常用。`

RGB和YUV之间可以转换。

视频编码的最主要目的是`数据压缩`，在传输过程中花费太多的带宽。
数据有冗余的，所以可以压缩

- 时间冗余：视频相邻的两帧之间内容相似，存在运动关系
- 空间冗余：视频的某一帧内部的相邻像素存在相似性
- 编码冗余：视频中不同数据出现的概率不同
- 视觉冗余：观众的视觉系统对视频中不同的部分敏感度不同


### 音视频

![gb2QgK](https://gitee.com/chasays/mdPic/raw/master/uPic/gb2QgK.jpg)

>封装格式（也叫容器），就是将已经编码压缩好的`视频轨`和`音频轨`按照`一定的格式放到一个文件中`，也就是说仅仅是一个外壳，或者大家把它当成一个放视频轨和音频轨的文件夹也可以。说得通俗点，视频轨相当于饭，而音频轨相当于菜，封装格式就是一个碗，或者一个锅，用来盛放饭菜的容器。 下面是几种常用的 视频文件后缀类型 与其相对应的 封装格式。

视频编码格式， 比较常见 `H264`。音频编码常见的 `aac`，和 `mp3`。 AAC压缩比高，音质更好。


[关于H264压缩技术](https://github.com/733gh/Android-Notes/blob/master/android/%E8%A7%86%E9%A2%91%E5%8E%8B%E7%BC%A9%E5%8E%9F%E7%90%86.md)

- 划分宏块 
- 划分子块 就是一个宏块里面再分成小块
- 帧分组 就是内容差不多的放在组里面，组成一个组称为GOP， 参考帧为IDR/I帧， 其他的为P/B帧。
- 运动估计与补偿 计算视频中物体的运动矢量，然后保存矢量和补偿
- 帧内预测 
先预测，就是分成宏块后然后几个模式预测，完了找到最`接近的预测模式`，得到预测图片A， A与原图对比相减，得到 `残差值。` 然后把残差和预测模式保存起来就可以恢复了。
- 优化：对残差数据做DCT 进一步压缩
- 再优化：CABAC无损压缩

 


# Audio
## 最基础
- 振幅， 保证输入的输出的在接受范围内
- 频率 同上
- dropout  输出不能有不连续的信号
- distortion 畸变，比如采样率发生变化 声音异常
- clipping  出现啸叫，顶峰没有数据

验证数据用的是

- 正玄波 sine-0db-1khz 这个主要是验证THD 和dropout
- step gain sine -60db~0db 可以验证clipping
- sweep 扫频 0khz~16Khz， 验证对每一个频率的处理
- speech， 验证distortion


## 影响声音质量的因素

- 压缩， 模拟型号转为数字pcm信号的时候就有丢失。提高采样率比如48k
- 延迟， 什么导致延迟，比如编码、打包、网络传输、jitter buffer
- jitter 抖动， udp传输 数据丢失，可以用jitter buffer来一职
- 丢包 udp传输，丢包是必然的 ，使用少量的丢包
- 回声 声学原因：布局、混响、延时大小、单双讲， 电学：信号干扰
- 其他噪音 




## 评估标准
- MOS 原始的就是一个主观的批判， 

根据提供给算法的信息，语音质量测试算法可分为两大类
1. `完全引用"（FR）`
算法可以访问并使用原始参考信号进行比较（即差异分析）。它可以比较参考信号的每个样本（说话端）与降级信号的每个对应样本（侦听器侧）。FR 测量可提供`最高的精度和可重复性`，但只能应用于实时网络中的专用测试（例如移动网络基准的驱动测试工具）

2. ` "无参考"（NR)`
算法仅使用降级信号进行质量评估，并且没有原始参考信号的信息。NR 算法只是`低准确性估计值`，因为源引用的原发语音特征（例如男性或女性说话者、背景噪音、非语音）完全未知。NR 算法的常见`变体甚至不分析解码的音频信号`，而是对 IP 数据包级别上的数字位流进行分析。因此，测量仅限于传输流分析。


`POLQA是最新的，其他都是小弟。`
![CzlEBt](https://gitee.com/chasays/mdPic/raw/master/uPic/CzlEBt.jpg)

- [PESQ](https://en.wikipedia.org/wiki/Perceptual_Evaluation_of_Speech_Quality)

Perceptual Evaluation of Speech Quality
PESQ结合了PSQM和 PAMS的优势，针对VoIP和混合的端到端应用作了改进，并针对MOS和MOS-LQ计算方法做了修改。
也是全参考测试算法
```py
## first, install soundfile and pypesq
import soundfile as sf
from pypesq import pesq

ref, sr = sf.read("sine.wav")
deg, sr = sf.read("sine1.wav")
score = pesq(ref, deg, sr)

print(111,score)
```

得到的score分数就是[MOS的值](https://en.wikipedia.org/wiki/Mean_opinion_score)

输出结果
```sh
4.5
```

计算方法
>https://pypi.org/project/pesq/




- POLQA PESQ的升级版 percptual objective listening quality analysis
```python
# here is the implementation
https://chromium.googlesource.com/external/webrtc/+/3c1e558449309be965815e1bf/webrtc/audio/test/low_bandwidth_audio_test.py
```

并扩展到处理更高带宽的音频信号。进一步的改进针对时间调用信号的处理，具有许多延迟变化。
全参考算法，在对参考和测试信号的相应摘录进行时间对齐后，对语音信号进行采样分析 ，对于端到端的质量评估。



- 其他
[ViSQOL (Virtual Speech Quality Objective Listener) is an objective](https://github.com/google/visqol) google的一个全参考

前面提到的都是全参考的, 无参考, 马上就来.看下面这个就是实际的应用.

[FAD 一种新颖的，无参考的音乐增强算法评估指标](https://github.com/google-research/google-research/tree/master/frechet_audio_distance)


```py

python -m frechet_audio_distance.compute_fad --background_stats stats/background_stats --test_stats stats/test1_stats
python -m frechet_audio_distance.compute_fad --background_stats stats/background_stats --test_stats stats/test2_stats
```
------
# Video

- [PEVA](https://en.wikipedia.org/wiki/Perceptual_Evaluation_of_Video_Quality)
测量算法可用于分析由数字视频编码/解码（或转码）过程、基于无线电或 IP 的传输网络和最终用户设备引起的可见伪影。
 
PEVQ还通过各种KPI（包括PSNR、失真指示器和唇部同步延迟）来量化视频信号中的异常。

psnr是“Peak Signal to Noise Ratio”的缩写，即峰值信噪比，是一种评价图像的客观标准，它具有局限性，一般是用于最大值信号和背景噪音之间的一个工程项目。

PSNR是最普遍，最广泛使用的评鉴画质的客观量测法，不过许多实验结果都显示，`PSNR的分数无法和人眼看到的视觉品质完全一致`，有可能PSNR较高者看起来反而比PSNR较低者差。这是因为人眼的视觉对于误差的敏感度并不是绝对的，其感知结果会受到许多因素的影响而产生变化

SSIM(structural similarity index)，结构相似性，是一种衡量两幅图像相似度的指标。该指标首先由德州大学奥斯丁分校的图像和视频工程实验室(Laboratory for Image and Video Engineering)提出。SSIM使用的两张图像中，一张为未经压缩的无失真图像，另一张为失真后的图像。

------
 
 # 应用

 [webrtc](https://github.com/webrtc)
 WebRTC是一个免费的开放软件项目，可通过简单的API为浏览器和移动应用程序提供实时通信（RTC）功能。 WebRTC组件已经过优化，可以最好地满足此目的。


 [testrtc](https://github.com/webrtc/testrtc)
 这里包含audio和video的测试和评估方法. 
------

mic 主要是针对audio
1. 能否2s静音
2. 计算RMS值
3. clip测试
4. 单双讲测试


------
camara  分2部分, 一个WxH分辨率(width x height) 和支持的分辨率

- 第一部分
1. 检查摄像机能否以请求的分辨率捕获 5 秒钟
1. 检查帧是冻结还是静音/黑色
1. 检测开始编码帧的时间
1. 报告编码时间和平均帧速率

- 第二部分

1. 列出似乎受支持的分辨率

------

网络
1. Udp/Tcp  `验证它可以使用给定协议与转服务器进行通信`
1. IPv6 连接  `验证它至少可以收集一个 IPv6 候选者`

------
连接性 (主要TURN server 连接的验证)

1. relay  可以通过 [TURN](https://webrtc.org/getting-started/turn-server) 服务器在对等体之间建立连接 
1. Reflexive 通过 NAT 验证对等体之间的连接
1. Host 验证可以在具有相同 IP 地址的对等体之间建立连接

------
 吞吐量
1. 数据吞吐量 `建立环回调用并测试链路上的数据通道吞吐量`

1. 视频带宽
- 建立环回呼叫并测试链路上的视频性能

- 在媒体通道上测量 `rtt(round-trip time)`.

- 测量带宽估计`性能（增加时间、最大值、平均值`）


>[py_quality_assessment](https://chromium.googlesource.com/external/webrtc/+/3c1e558449309be965815e1bf/webrtc/modules/audio_processing/test/py_quality_assessment/)


# read more
- https://ai.googleblog.com/2019/10/audio-and-visual-quality-measurement.html
- https://blog.csdn.net/foolskindom/article/details/5155587?utm_medium=distribute.pc_relevant.none-task-blog-title-3&spm=1001.2101.3001.4242
- https://blog.csdn.net/sinat_35821976/article/details/80081176
- https://wenku.baidu.com/view/33e49080d0d233d4b14e697d.html
