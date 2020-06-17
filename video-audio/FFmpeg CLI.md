# FFmpeg CLI 工具的使用

- [FFmpeg CLI 工具的使用](#ffmpeg-cli-工具的使用)
  - [ffmpeg](#ffmpeg)
    - [详细参数说明](#详细参数说明)
  - [ffplay](#ffplay)
    - [快捷键](#快捷键)
    - [选项](#选项)
  - [ffprobe](#ffprobe)
  - [ffmpeg使用](#ffmpeg使用)
    - [H.264 视频编码](#h264-视频编码)
      - [Constant Rate Factor (CRF)](#constant-rate-factor-crf)
      - [Two-Pass ABR](#two-pass-abr)
    - [AAC 音频编码](#aac-音频编码)
    - [Streaming](#streaming)
- [参考资料](#参考资料)

## ffmpeg

ffmpeg是用于转码的应用程序。一个简单的转码命令可以这样写：

```shell
# 将input.avi转码成output.ts，并设置视频的码率为640kbps
$ ffmpeg -i input.avi -b:v 640k output.ts
```

### 详细参数说明

**通用选项**
```shell
-L                  license
-h                  帮助
-fromats            显示可用的格式，编解码的，协议的...
-f fmt              强迫采用格式fmt
-i filename         输入文件
-y                  覆盖输出文件
-t duration         设置纪录时间 hh:mm:ss[.xxx]格式的记录时间也支持
-ss position        搜索到指定的时间 [-]hh:mm:ss[.xxx]的格式也支持
-title string       设置标题
-author string      设置作者
-copyright string   设置版权
-comment string     设置评论
-target type        设置目标文件类型(vcd,svcd,dvd)所有的格式选项（比特率，编解码以及缓冲区大小）自动设置，只需要输入如下的就可以了：ffmpeg -i myfile.avi -target vcd /tmp/vcd.mpg
-hq                 激活高质量设置
-itsoffset offset   设置以秒为基准的时间偏移，该选项影响所有后面的输入文件。该偏移被加到输入文件的时戳，定义一个正偏移意味着相应的流被延迟了 offset秒。[-]hh:mm:ss[.xxx]的格式也支持
```

**视频选项**
```shell
-b bitrate          设置比特率，缺省200kb/s
-r fps              设置帧频 缺省25
-s size             设置帧大小 格式为WXH 缺省160X128.下面的简写也可以直接使用：Sqcif 128X96 qcif 176X144 cif 252X288 4cif 704X576
-aspect aspect      设置横纵比 4:3 16:9 或 1.3333 1.7777
-croptop size       设置顶部切除带大小 像素单位
-cropbottom size 
–cropleft size 
–cropright size
-padtop size        设置顶部补齐的大小 像素单位
-padbottom size 
–padleft size 
–padright size 
–padcolor color     设置补齐条颜色(hex,6个16进制的数，红:绿:兰排列，比如000000代表黑色)
-vn                 不做视频记录
-bt tolerance       设置视频码率容忍度kbit/s
-maxrate bitrate    设置最大视频码率容忍度
-minrate bitreate   设置最小视频码率容忍度
-bufsize size       设置码率控制缓冲区大小
-vcodec codec       强制使用codec编解码方式。如果用copy表示原始编解码数据必须被拷贝。
-sameq              使用同样视频质量作为源（VBR）
-pass n             选择处理遍数（1或者2）。两遍编码非常有用。第一遍生成统计信息，第二遍生成精确的请求的码率
-passlogfile file   选择两遍的纪录文件名为file
```

**高级视频选项**

```shell
-g gop_size                 设置图像组大小
-intra                      仅适用帧内编码
-qscale q                   使用固定的视频量化标度(VBR)
-qmin q                     最小视频量化标度(VBR)
-qmax q                     最大视频量化标度(VBR)
-qdiff q                    量化标度间最大偏差 (VBR)
-qblur blur                 视频量化标度柔化(VBR)
-qcomp compression          视频量化标度压缩(VBR)
-rc_init_cplx complexity    一遍编码的初始复杂度
-b_qfactor factor           在p和b帧间的qp因子
-i_qfactor factor           在p和i帧间的qp因子
-b_qoffset offset           在p和b帧间的qp偏差
-i_qoffset offset           在p和i帧间的qp偏差
-rc_eq equation             设置码率控制方程 默认tex^qComp
-rc_override override       特定间隔下的速率控制重载
-me method                  设置运动估计的方法 可用方法有 zero phods log x1 epzs(缺省) full
-dct_algo algo              设置dct的算法 可用的有 0 FF_DCT_AUTO 缺省的DCT 1 FF_DCT_FASTINT 2 FF_DCT_INT 3 FF_DCT_MMX 4 FF_DCT_MLIB 5 FF_DCT_ALTIVEC
-idct_algo algo             设置idct算法。可用的有 0 FF_IDCT_AUTO 缺省的IDCT 1 FF_IDCT_INT 2 FF_IDCT_SIMPLE 3 FF_IDCT_SIMPLEMMX 4 FF_IDCT_LIBMPEG2MMX 5 FF_IDCT_PS2 6 FF_IDCT_MLIB 7 FF_IDCT_ARM 8 FF_IDCT_ALTIVEC 9 FF_IDCT_SH4 10 FF_IDCT_SIMPLEARM
-er n                       设置错误残留为n 1 FF_ER_CAREFULL 缺省 2 FF_ER_COMPLIANT 3 FF_ER_AGGRESSIVE 4 FF_ER_VERY_AGGRESSIVE
-ec bit_mask                设置错误掩蔽为bit_mask,该值为如下值的位掩码 1 FF_EC_GUESS_MVS (default=enabled) 2 FF_EC_DEBLOCK (default=enabled)
-bf frames                  使用frames B 帧，支持mpeg1,mpeg2,mpeg4
-mbd mode                   宏块决策 0 FF_MB_DECISION_SIMPLE 使用mb_cmp 1 FF_MB_DECISION_BITS 2 FF_MB_DECISION_RD
-4mv                        使用4个运动矢量 仅用于mpeg4
-part                       使用数据划分 仅用于mpeg4
-bug param                  绕过没有被自动监测到编码器的问题
-strict strictness          跟标准的严格性
-aic                        使能高级帧内编码 h263+
-umv                        使能无限运动矢量 h263+
-deinterlace                不采用交织方法
-interlace                  强迫交织法编码仅对mpeg2和mpeg4有效。当你的输入是交织的并且你想要保持交织以最小图像损失的时候采用该选项。可选的方法是不交织，但是损失更大
-psnr                       计算压缩帧的psnr
-vstats                     输出视频编码统计到vstats_hhmmss.log
-vhook module               插入视频处理模块 module 包括了模块名和参数，用空格分开
```

**音频选项**
```shell
-ab bitrate                 设置音频码率
-ar freq                    设置音频采样率
-ac channels                设置通道 缺省为1
-an                         不使能音频纪录
-acodec codec               使用codec编解码
```
**音频/视频捕获选项**
```shell
-vd device                  设置视频捕获设备。比如/dev/video0
-vc channel                 设置视频捕获通道 DV1394专用
-tvstd standard             设置电视标准 NTSC PAL(SECAM)
-dv1394                     设置DV1394捕获
-av device                  设置音频设备 比如/dev/dsp
```
**高级选项**
```shell
-map file:stream            设置输入流映射
-debug                      打印特定调试信息
-benchmark                  为基准测试加入时间
-hex                        倾倒每一个输入包
-bitexact                   仅使用位精确算法 用于编解码测试
-ps size                    设置包大小，以bits为单位
-re                         以本地帧频读数据，主要用于模拟捕获设备
-loop                       循环输入流（只工作于图像流，用于ffserver测试）
```
## ffplay

ffplay是ffmpeg工程中提供的播放器，功能相当的强大，凡是ffmpeg支持的视音频格式它基本上都支持。甚至连VLC不支持的一些流媒体都可以播放（比如说RTMP），但是它的缺点是其不是图形化界面的，必须通过键盘来操作。一个简单的播放命令可以这样写：

```shell
# 播放test.avi
$ ffplay test.avi
```

### 快捷键

播放视音频文件的时候，可以通过下列按键控制视音频的播放

按键 | 作用
---|---
Q,ESC               | 退出
F                   | 全屏
P,Space             | 暂停
W                   | 显示音频波形
S                   | 逐帧显示
左方向键/右方向键   | 向后10s/向前10s
上方向键/下方向键   | 向后1min/向前1min
page down/page up   | 向后10min/向前10min
鼠标点击屏幕        | 跳转到指定位置（根据鼠标位置相对屏幕的宽度计算）

### 选项

```shell
-x width                强制屏幕宽度
-y height               强制屏幕高度
-s size                 强制屏幕大小
-fs                     全屏
-an                     关闭音频
-vn                     关闭视频
-ast stream             设置想播放的音频流（需要指定流ID）
-vst stream             设置想播放的视频流（需要指定流ID）
-sst stream             设置想播放的字幕流（需要指定流ID）
-ss second              从指定位置开始播放，单位是秒
-t time                 播放指定时长的视频
-nodisp                 无显示屏幕
-f                      强制封装格式
-pix_fmt                指定像素格式
-stats                  显示统计信息
-idct                   IDCT算法
-ec                     错误隐藏方法
-sync                   视音频同步方式（type=audio/video/ext）
-autoexit               播放完成自动退出
-exitonkeydown          按下按键退出
-exitonmousedown        按下鼠标退出
-loop count             指定循环次数
-framedrop              CPU不够的时候丢帧
-window_title           显示窗口的标题
-rdftspeed              Rdft速度
-showmode               显示方式(0 = video, 1 = waves, 2 = RDFT)
-codec                  强制解码器
```

## ffprobe

ffprobe是用于查看文件格式的应用程序。

## ffmpeg使用

### H.264 视频编码

通常建议使用两种速率控制模式：Constant Rate Factor(CRF) 或 Two-Pass ABR，速率控制决定每个帧将使用多少位，这将确定文件大小以及质量分配方式。这里不进行详述，具体可以参考 [这里](https://slhck.info/video/2017/03/01/rate-control.html)

#### Constant Rate Factor (CRF)

#### Two-Pass ABR

### AAC 音频编码

### Streaming

# 参考资料
- [[总结]FFMPEG视音频编解码零基础学习方法](https://blog.csdn.net/leixiaohua1020/article/details/15811977)
- [FFmpeg Documentation](https://ffmpeg.org/documentation.html)
- [FFmpeg Wiki](https://trac.ffmpeg.org/)
- [FFmpeg H.264 Video Encoding Guide](https://trac.ffmpeg.org/wiki/Encode/H.264)
- [FFmpeg AAC Encoding Guide](https://trac.ffmpeg.org/wiki/Encode/AAC)
- [FFmpeg Encoding for Streaming Sites](https://trac.ffmpeg.org/wiki/EncodingForStreamingSites)
- [FFmpeg Streaming Guide](https://trac.ffmpeg.org/wiki/StreamingGuide)
- [Understanding Rate Control Modes (x264, x265, vpx)](https://slhck.info/video/2017/03/01/rate-control.html)