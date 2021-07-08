>  原文地址 [learnku.com](https://learnku.com/laravel/t/327/using-ffmpeg-to-deal-with-multimedia-files-under-laravel)



说明[#](#f411d0)
--------------

最近在项目里使用 [FFmpeg](https://www.ffmpeg.org/) 做音频格式转换，这里做个笔记.

### 什么是 FFmpeg?[#](#d32201)

> *   FFmpeg 是一个自由软件，可以运行音频和视频多种格式的录影、转换、流功能，包含了 libavcodec ─这是一个用于多个项目中音频和视频的解码器库，以及 libavformat—— 一个音频与视频格式转换库。
> *   这个项目最初是由 Fabrice Bellard 发起的，而现在是由 Michael Niedermayer 在进行维护。许多 FFmpeg 的开发者同时也是 MPlayer 项目的成员，FFmpeg 在 MPlayer 项目中是被设计为服务器版本进行开发。
> *   -- 上面的解释引自 [Wiki](http://zh.wikipedia.org/wiki/FFmpeg)

### 应用领域[#](#59fcbf)

*   FFmpeg 被许多开源项目采用，比如 ffmpeg2theora, VLC, MPlayer, HandBrake, Blender, Google Chrome 等。
*   还有 DirectShow/VFW 的 ffdshow (external project) 和 QuickTime 的 Perian (external project) 也采用了 FFmpeg。
*   KMPlayer、格式工厂、暴风影音、QQ 影音、KMP、GOM Player、PotPlayer（2010）都在其列。

安装 FFmpeg 命令[#](#52d3a3)
------------------------

下面是 Ubuntu 14 LTS 的安装方法:

```
sudo apt-add-repository ppa:mc3man/trusty-media
sudo apt-get update
sudo apt-get install -y ffmpeg gstreamer0.10-ffmpeg

```

安装成功后测试:

[![](https://cdn.learnku.com//uploads/images/201412/26/1/NwdDkywUzu.png)](https://cdn.learnku.com//uploads/images/201412/26/1/NwdDkywUzu.png)

PHP-FFMpeg[#](#11f5ba)
----------------------

[PHP-FFMpeg](https://github.com/PHP-FFMpeg/PHP-FFMpeg) 是 FFMpeg 和 FFProbe 命令的 PHP 面对对象 Wrapper.

### 安装[#](#e655a4)

项目根目录下:

```
$ composer require "php-ffmpeg/php-ffmpeg:~0.5"

```

### 使用[#](#ecff77)

创建 `FFMpeg\FFMpeg` 对象:

```
$ffmpeg = FFMpeg\FFMpeg::create(array(
    'ffmpeg.binaries'  => '/opt/local/ffmpeg/bin/ffmpeg',
    'ffprobe.binaries' => '/opt/local/ffmpeg/bin/ffprobe',
    'timeout'          => 3600, // The timeout for the underlying process
    'ffmpeg.threads'   => 12,   // The number of threads that FFMpeg should use
), $logger);

```

### 视频处理[#](#194830)

#### 视频转码[#](#b73d52)

```
$video = $ffmpeg->open('video.mpeg');

$format = new Format\Video\X264();
$format->on('progress', function ($video, $format, $percentage) {
    echo "$percentage % transcoded";
});

$format
    -> setKiloBitrate(1000)          // 视频码率
    -> setAudioChannels(2)        // 音频声道
    -> setAudioKiloBitrate(256); // 音频码率

// 保存为
$video->save($format, public_path().'/uploads/video/video.avi');

```

#### 视频截图[#](#64b60d)

```
// 在视频 42 秒的地方截图
$frame = $video->frame(FFMpeg\Coordinate\TimeCode::fromSeconds(42));
$frame->save('image.jpg');

```

#### 视频旋转[#](#253fa0)

```
$video->filters()->rotate($angle)->synchronize();

```

`$angle` 参数的选项从下面选取:

*   FFMpeg\Filters\Video\RotateFilter::ROTATE_90 : 90° 顺时针方向
*   FFMpeg\Filters\Video\RotateFilter::ROTATE_180 : 180°
*   FFMpeg\Filters\Video\RotateFilter::ROTATE_270 : 90° 逆时针方向

`synchronize` 方法是触发保存的动作，因为 `filters` 可以多个链接起来，如下:

```
$video
    ->filters()
    ->resize($dimension, $mode, $useStandards)
    ->framerate($framerate, $gop)
    ->synchronize();

```

#### 调整视频尺寸[#](#4adc06)

```
$video->filters()->resize($dimension, $mode, $useStandards);

```

传参见: [文档](https://github.com/PHP-FFMpeg/PHP-FFMpeg#resize)

#### 视频帧速率[#](#ea8ba7)

```
$video->filters()->framerate($framerate, $gop);
```

传参见: [文档](https://github.com/PHP-FFMpeg/PHP-FFMpeg#framerate)

#### 视频裁剪[#](#cc7737)

```
$video->filters()->clip(FFMpeg\Coordinate\TimeCode::fromSeconds(30), FFMpeg\Coordinate\TimeCode::fromSeconds(15));
```

传参见: [文档](https://github.com/PHP-FFMpeg/PHP-FFMpeg#clip)

### 音频处理[#](#a58fdb)

#### 音频格式转换[#](#14ea64)

```
$audio = $ffmpeg->open('track.mp3');$format = new FFMpeg\Format\Audio\Flac();$format->on('progress', function ($audio, $format, $percentage) {    echo "$percentage % transcoded";});$format    -> setAudioChannels(2)    -> setAudioKiloBitrate(256);$audio->save($format, public_path().'/uploads/video/track.flac');
```

#### 音频采样率[#](#eb4c87)

```
$audio->filters()->resample(16000);
```

### 音频转换示例[#](#fcc27c)

在我的项目中，需要把 `8khz 采样率, 单声道的 amr 文件` 转换为 `16khz 采样率, 单声道的 wav 文件` , 下面是实例代码:

```
$audio_path = public_path().'/uploads/audio/';$random_string = str_random(10);$amr_filename = $random_string.'.amr';$wav_filename = $random_string.'.wav';Input::file('audio_file')->move($audio_path, $amr_filename);$audio = $ffmpeg->open($audio_path.$amr_filename);$format = new FFMpeg\Format\Audio\Wav();$format->setAudioChannels(1);$audio->filters()->resample('16000');$audio->save($format, $audio_path.$wav_filename);
```

总结[#](#25f9c7)
--------------

利用此工具，可以对多媒体文件进行自由修改，在很多应用场景下都能应用上，把 TA 放到你的工具箱里面吧 ![](https://cdn.learnku.com/assets/images/twemoji/sparkles.png) .

另外，还有一个很棒的项目 [sonus](https://github.com/rafasamp/sonus), 专门为 Laravel 框架而写的，唯一遗憾的是未能支持 [filters](http://ffmpeg.mplayerhq.hu/ffmpeg-filters.html).

摈弃世俗浮躁，追求技术精湛

