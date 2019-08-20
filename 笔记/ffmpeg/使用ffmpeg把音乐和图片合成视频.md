# 使用ffmpeg把音乐和图片合成视频

* 第一步：先将视频中原本的背景声音去掉，语句如下：
> ffmpeg.exe -i input.mp4 -c:v copy -an  input-no-audio.mp4

* 第二步：将去除背景声音的视频文件添加音频，语句如下：
> ffmpeg.exe -i input.mp4 -i 音乐.mp3 -t 25 -y 新视频.mp4