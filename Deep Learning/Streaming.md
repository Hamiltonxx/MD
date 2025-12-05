# Live Streaming
The key consideration when streaming media to a browser is the fact that rather than playing a finite file we are relaying a file that is being created on the fly and has no pre-determined start or end.
# Adaptive Streaming
One of the main priorities for live streaming is to keep the player synchronized with the stream. The data transfer rate is monitored and if it looks like it's not keeping up,we drop down to a lower bandwidth/quality stream.
# Streaming Protocols
While static media is usually served over HTTP, there are several protocols for serving adaptive streams.
# Video Streaming File Format
## MPEG-DASH  
This approach allows us to download chunks of the video stream using XHR and "append" the chunks to the stream that's played by the 'video' element.
## HLS
Http Live Streaming
# Server-side Streaming Technology
## GStreamer


# DASH
基于HTTP的动态自适应流(Dynamic Adaptive Streaming over HTTP)是一个自适应流协议。它允许视频流基于网络性能切换比特率，来保持视频播放。
```shell
ffmpeg -i Test_15MB.mp4 -c:v libvpx-vp9 -keyint_min 150 -g 150 -tile-columns 4 -frame-parallel 1 -f webm -dash 1 -an -vf scale=160:90 -b:v 250k -dash 1 video_160x90_250k.webm
```
```shell
ffmpeg -i Test_15MB.mp4 -c:v libvpx-vp9 -keyint_min 150 \
-g 150 -tile-columns 4 -frame-parallel 1 -f webm -dash 1 \
-an -vf scale=160:90 -b:v 250k -dash 1 video_160x90_250k.webm \
-an -vf scale=320:180 -b:v 500k -dash 1 video_320x180_500k.webm \
-an -vf scale=640:360 -b:v 750k -dash 1 video_640x360_750k.webm \
-an -vf scale=640:360 -b:v 1000k -dash 1 video_640x360_1000k.webm \
-an -vf scale=1280:720 -b:v 1500k -dash 1 video_1280x720_1500k.webm
```
```shell
ffmpeg -i Test_15MB.mp4 -vn -acodec libvorbis -ab 128k -dash 1 my_audio.webm
```
```shell
ffmpeg \
  -f webm_dash_manifest -i video_160x90_250k.webm \
  -f webm_dash_manifest -i video_320x180_500k.webm \
  -f webm_dash_manifest -i video_640x360_750k.webm \
  -f webm_dash_manifest -i video_1280x720_1500k.webm \
  -f webm_dash_manifest -i my_audio.webm \
  -c copy \
  -map 0 -map 1 -map 2 -map 3 -map 4 \
  -f webm_dash_manifest \
  -adaptation_sets "id=0,streams=0,1,2,3 id=1,streams=4" \
  my_video_manifest.mpd
```

# Dash from RTSP
Use ffmpeg to connect to an ip camera and create video files on the fly that can be viewed in an mpeg-dash compatible browser using dash.js and an html5 video element.
```shell
sudo su
```
```shell
ffmpeg -i 'rtsp://admin:Yijian@2018!@192.168.40.105:554/h264/ch1/main/av_stream' -an -c:v copy -b:v 2000k -f dash -window_size 4 -extra_window_size 0 -remove_at_exit 1 mpeg-dash/manifest.mpd

ffmpeg -i 'rtsp://admin:Yijian@2018!@192.168.40.105:554/h264/ch1/main/av_stream' -an -c:v copy -b:v 2000k -f dash -window_size 4 -extra_window_size 0 -min_seg_duration 2000000 -remove_at_exit 1 /var/www/html/mpeg-dash/manifest.mpd
```
```html
<!doctype html>
<html>
    <head>
        <title>Dash.js Rocks</title>
        <style>
            video {
                width: 640px;
                height: 360px;
            }
        </style>
    </head>
    <body>
        <div>
            <video id="videoPlayer" controls></video>
        </div>
        <script src="yourPathToDash/dash.all.min.js"></script>
        <script>
            (function(){
                var url = "https://dash.akamaized.net/envivio/EnvivioDash3/manifest.mpd";
                var player = dashjs.MediaPlayer().create();
                player.initialize(document.querySelector("#videoPlayer"), url, true);
            })();
        </script>
    </body>
</html>
```

# Stream live video
To speed up and to protect SD Card, it is better to write video chunks to RAM memory. When the number of chuncks go high, clear the old ones to get more space.
```shell
ffmpeg -y -i 'rtsp://admin:Yijian@2018!@192.168.40.105:554/h264/ch1/main/av_stream' -c:v copy -f dash -seg_duration 1 -streaming 1 -window_size 30 -remove_at_exit 1 live.mpd
```
```shell
ffmpeg -hide_banner -y -rtsp_transport tcp -use_wallclock_as_timestamps 1 -i 'rtsp://admin:Yijian@2018!@192.168.40.105:554/h264/ch1/main/av_stream' -c:v copy -f dash -window_size 4 live.mpd
```

# HLS
```shell
#!/bin/bash
VIDSOURCE="rtsp://admin:Yijian@2018!@192.168.40.105:554/h264/ch1/main/av_stream"
AUDIO_OPTS="-c:a aac -b:a 160000 -ac 2"
VIDEO_OPTS="-s 854x480 -c:v libx264 -b:v 800000"
OUTPUT_HLS="-hls_time 10 -hls_list_size 10 -start_number 1"
ffmpeg -i "$VIDSOURCE" -y $AUDIO_OPTS $VIDEO_OPTS $OUTPUT_HLS playlist.m3u8
```

# Python
```python
@app.route("/hls/<filename>", methods=["GET"])
def m3u8file(filename):
    return send_file(f'/home/aiserver/projects/gpuservice/hls/{filename}')
```