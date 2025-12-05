# 先上结果
我们把经典科幻电影《普罗米修斯》抽一些帧出来，做成动画。我们截取了30:52 ~ 31:57的一千多帧，删掉一些主题无关的帧后，合成gif。  
来欣赏下他们对未知空间"走廊"做测绘，是否像极了我们测绘工作的终极目标。  
![https://pro-developer.oss-cn-shanghai.aliyuncs.com/img/prometheus.gif](https://pro-developer.oss-cn-shanghai.aliyuncs.com/img/prometheus.gif)
# 简介 
FFmpeg = Fast Forwarding Moving Picture Expert Group  
是一个非常快的视频音频转换器，支持live的视频音频源。如果要创建视频相关、流媒体相关网站，那你就必须得学习了解FFMPEG.
# Basic Syntax
```shell
ffmpeg -i mysong.wav mysong.mp3
# with a standard loudness normalizer filter
# -af,audio filter;-c:a,codec audio;-b:a bitrate audio;-ar audio rate/sampling rate
ffmpeg -i mysong.wav -af loudnorm -c:a mp3 -b:a 256k -ar 48000 mysong.mp3

ffmpeg -i master.mov master.mp4
# -t,process only for a specified duration;-ac,audio channels
ffmpeg -i https://www.dropbox.com/master.mov -t 00:02:30 -c:v h264 -b:v 3M -c:a aac -b:a 1128k -ar 44100 -ac 2 master.mp4
```
# Keyframes
I-frame(intraframe): stores the whole picture;  
P-frame(predictive frame): stores only the change against previous one;  
B-frame(bi-directional frame): stores differences against previous or future ones;  
Typically, the I-frames are interspersed with P-frames and B-frames in a compressed video. The more I-frames that are contained the better quality the video will be.
## Keyframe Interval
If you need to stream a file at 25FPS, using the h264 codec, a good idea will be to specify a keyframe interval of 2 seconds.
```shell
# -key_int_min,minimum keyframe interval every 25 frames; -g,group of pictures 25FPSx2seconds; -sc_threshold,Scene Change
ffmpeg -i YOUR_INPUT -c:v h264 -key_int_min 25 -g 50 -sc_threshold 0 OUTPUT.mp4
```
The reason why a short keyframe interval is very important is because we need to provide the end user with a fast experience during playback or seeking a video.
```shell
# check the keyframe interval
ffprobe -loglevel error -skip_frame nokey -select_streams v:0 -show_entries frame=pkt_pts_time of csv=print_section=0 YOUR_FILE.mp4
```
# Metadata and FFProbe
metadata, a set of data that describes and gives information about other data.
```shell
ffprobe -v error -select_streams v:0 -show_entries stream=duration -of default=noprint_wrappers=1:nokey=1 -sexagesimal YOUR_INPUT.mp4
# -show_entries stream=duration: instruct FFPROBE to display the duration of the stream
# -of default=noprint_wrappers=1:nokey=1: display only the requested information
# -sexagesimal: in sexagesimal format(base 60) (HH:MM:SS:ms)
ffprobe YOUR_INPUT.mp4 > output.txt 2>&1 # EXTRACT the data into a Text file
ffprobe -v quiet -print_format json -show_format -show_streams YOUR_INPUT.mp4 > YOUR_INPUT.json # export to json
```
# Extract
```shell
# extract metadata
ffmpeg -i YOUR_INPUT -f ffmetadata out.txt

# extract video and audio
ffmpeg -i home.mov -map 0 -c:a aac -c:v h265 YOUR_OUTPUT.mkv

# extract video only
ffmpeg -i YOUR_VIDEO -an -c:v copy OUTPUT # -an,no audio; -c:v copy,without re-encoding
ffmpeg -i YOUR_VIDEO.mov -an YOUR_VIDEO_WITHOUT_AUDIO.h265
```
# Cutting Videos
Extract portions of video with the Seeking function. -ss(before -i) -- In marker, -t -- Out marker. If you don't want re-encoding, use the -c copy option.
```shell
ffmpeg -ss 00:01:30.000 -i YOUR_VIDEO.mp4 -t 00:00:20.000 -c copy YOUR_EXTRACTED_VIDEO.mp4
```
# Producing h264/AVC videos
```shell
ffmpeg -i your_video.mov -c:v h264 -crf 23 -tune film your_output.mp4
ffmpeg -i your_source_video -c:v h264 -preset medium -tune film -crf 23 -c:a copy output.mpt4
```
If you're looking for an output that is roughly "visually lossless" but not technically lossless, use a -crf value of around 17 or 18.
# Producing h265/HEVC videos
To encode video in h265 format FFMPEG needs to be built with the --enable-gpl --enable-libx265 configuration flags and requires x265 to be installed on your system.
```shell
ffmpeg -i input -c:v libx265 -crf 28 -c:a aac -b:a 128k output.mp4

ffmpeg -y -i input -c:v libx265 -b:v 2600k -x265-params pass=1 -an -f mp4 /dev/null && \
ffmpeg -i input -c:v libx265 -b:v 2600k -x265-params pass=2 -c:a aac -b:a 128k output.mp4
```
# h266/VVC - Versatile Video Codec
H265/HEVC requires 10 gigabytes of data to transimit a 90-min UHD video. With H266,only 5 gigabytes of data are required to achieve the same quality.  Because H266/VVC was developed with ultra-high-resolution video content in mind,the new standard is particularly beneficial when streaming 4K or 8K videos on a flat screen TV. Furthermore,H266/VVC is ideal for all types of moving images:from high-resolution 360 video panoramas to screen sharing contents.  
# Producing VP8 Videos
```shell
ffmpeg -i INPUT -c:v libvpx -b:v 1M -c:a libvorbis OUTPUT.webm
ffmpeg -i INPUT -c:v libvpx -crf 10 -b:v 1M -c:a libvorbis OUTPUT.webm
```
Choose a higher bit rate if you want better quality.
# Producing VP9 videos
VP9 is the direct competitor of h265,mainly used for YouTube platform.
```shell
ffmpeg -i input.mp4 -c:v libvpx-vp9 -b:v 2M output.webm

# Targeting Bitrate Mode
ffmpeg -i input.mp4 -c:v libvpx-vp9 -b:v 2M -pass 1 -an -f webm /dev/null&&ffmpeg -i input.mp4 -c:v libvpx-vp9 -b:v 2M pass 2 -c:a libopus output.webm
# Constant Quality
ffmpeg -i input.mp4 -c:v libvpx-vp9 -b:v 0 -crf 30 -pass 1 -an -f webm /dev/null&&ffmpeg -i input.mp4 -c:v libvpx-vp9 -b:v 0 -crf 30 -pass 2 -c:a libopus output.webm
```
# The OPUS Audio Codec
Opus is unmatched for interactive speech and music transmission over the Internet, but is also intended for storage and streaming applications.
```shell
ffmpeg -i INPUT.aiff -c:a libopus -b:a 96k -ac 2 OUTPUT.opus
ffmpeg -i INPUT.ac3 -c:a libopus -af channelmap=channel_layout=5.1 -b:a 96k OUPUT.opus
ffmpeg -i INPUT.ac3 -c:a libopus -ac 2 -b:a 96k OUTPUT.opus
ffmpeg -i YOUR_OPUS_FILE.opus -c:a copy OUTPUT.caf # Embed in Safari/iOS
```
# The FLAC Audio Codec
Free Lossless AUdio Codec, an audio format similar to MP3, but lossless.  
```
ffmpeg -i YOUR_AUDIO.wav -c:a flac OUTPUT.flac
```
# Producing AV1 Video
Alliance for Open Media Video 1  
It's considered as a successor of VP9 codec, and used with the Opus Codec for audio and "packed" into a WebM container, so to be used in HTML5 video and used as a WebRTC standard.  
You can deliver high-quality SD and HD video to mobile and other devices over congested or bandwidth-constrained networks at bitrates unachievable with traditional codecs.
```shell
ffmpeg -i YOUR_INPUT -c:v libaom-av1 -cpu-used 8 -crf 27 -b:v 0 -strict experimental -c:a libopus -b:a 128k -movflags +faststart YOUT_OUTPUT.webm
```
# SVT-AV1
Defined as the Scalable AV1 technology this codec can be faster than the one used on the LibAOM library, especially on x86(Intel) processors.
# AV1AN
A Python tool.  
An easy way to start using AV1/VP9/VP8 encoding.
```shell
av1an -i YOUR_INPUT -enc aom -v "--cpu-used=3 --end-usage=q --cq-level=30 --threads=8" -w 10 -p 2 -ff " -vf scale=-1:720" -a " -c:a libopus -b:a 24k" -s scenes.csv -log my_log -o YOUT_OUTPUT 
```
# Streaming on Social Media with RTMP
Real-Time-Messaging-Protocol
## Streaming Pre-Recorded Content
We'll explore 2 of the methods to stream a pre-recorded video to a RTMP server.  
The first way is to take your input "as-is" and convert it in real-time to FLV. This method uses a lot of CPU, because of real-time conversion.
Streaming for YouTube Live requirements:
```
Resolution: 1920x1080
Video Bitrate Range: 3000~6000Kbps
Video Codec: H.264, Level 4.1 for up to 1080p 30FPS or Level 4.2 for 60FPS
Keyframe Frequency: 2 seconds
Audio Codec: AAC or Mp3
Bitrate Encoding: CBR(Constant Bit Rate)
```
```shell
ffmpeg -re -i INPUT.mp4 -c:v libx264 -profile:v high -level:v 4.1 -preset veryfast -b:v 3000k -maxrate 3000k -bufsize 6000k -pix_fmt yuv420p -g 50 -keyint_min 50 -sc_threshold 0 -c:a aac -b:a 128k -ac 2 -ar 44100 -f flv "rtmp://a.rtmp.youtube.com/live2/YOUR_STREAMING_KEY"
```
If machine is slow and just can't handle the real-time encoding process, Full-HD videos or 4K or above, you can always pre-process the file before streaming it.
```shell
ffmpeg -i INPUT.mp4 -c:v libx264 -profile:v high -level:v 4.1 -preset veryfast -b:v 3000k -maxrate 3000k -bufsize 6000k -pix_fmt yuv420p -g 50 -keyint-min 50 -sc_threshold 0 -c:a aac -b:a 128k -ac 2 -ar 44100 OUTPUT.mp4
ffmpeg -re -i OUTPUT.mp4 -c copy -f flv "rtmp://a.rtmp.youtube.com/live2/YOUR_STREAMING_KEY"
```
# Re-Stream to multiple destinations
-tee option, write the same data to several outputs.
```shell
ffmpeg -i INPUT -c:v h264 -c:a aac -f tee -map 0:v -map 0:a "output.mkv | [f=mpegts]udp://10.0.1.255:1234/"
```
# Concatenate Video Playlists
Concatenate multiple videos in order to stream a playlist of different content to a social media destination usch as YouTube Live,Facebook Live,Twitch,etc.  
4 steps:  
1) Conform your files to the platform's tech specs;  
2) Segment the files in HLS format with Bento4;
3) Create a playlist in M3U8 format;
4) Stream the M3U8 playlist to the RTMP server;
```shell
ffmpeg -i YOUR_INPUT -c:v libx264 -preset veryfast -b:v 3000k -maxrate 3000k -bufsize 6000k -pix_fmt yuv420p -g 50 -keyint_min 50 -sc_threshold 0 -c:a aac -b:a 128k -ac 2 -ar 44100 OUTPUT.mp4
mp42hls --output-single-file OUTPUT.mp4
ffmpeg -re -i stream.m3u8 -c copy -f flv "rtmp://a.rtmp.youtube.com/live2/YOUR_STREAMING_KEY"
nano playlist.txt
  file 'video1/stream.m3u8'
  file 'video2/stream.m3u8'
  file 'video3/stream.m3u8'
  file 'video4/stream.m3u8'
  file 'video5/stream.m3u8'
ffmpeg -re -f concat -i playlist.txt -c copy -f flv "rtmp://a.rtmp.youtube.com/live2/YOUR_STREAMING_KEY"
```
# Producing HLS with FFMPEG Bento4
With FFMPEG and Bento4 you can create standard HLS streaming formats. HLS is a standard created by Apple in order to deliver Audio/Video streaming using ordinary web servers and content delivery networks(CNDs).
HLS supports:
1. Live broadcasts and pre-recorded content(VOD)  
2. Multiple alternate streams at different bit rates  
3. Intelligent switching of streams in response to network bandwidth changes  
4. Media encryption and user authentication  
In order to prepare a video into a standard HLS stream, you will need both FFMPEG and bento4 installed.
```shell
brew install bento4
```
```shell
ffmpeg -i MASTER.mp4 -c:v h264 -crf 22 -tune film -profile:v main -level:v 4.0 -maxrate 5000k -bufsize 10000k -r 25 -keyint_min 25 -g 50 -sc_threshold 0 -c:a aac -ar 44100 -b:a 128k -ac 2 -pix_fmt_yuv420p -movflags +faststart 1080.mp4 \
-s 1280x720 -c:v h264 -crf 24 -tune film -profile:v main -level:v 4.0 -maxrate 2500k -bufsize 5000k -r 25 -keyint_min 25 -g 50 -sc_threshold 0 -c:a aac -ar 44100 -b:a 128k -ac 2 -pix_fmt yuv420p -movflags + faststart 720.mp4 \
-s 854x480 -c:v h264 -crf 30 -tune film -profile:v main -level:v 4.0 -maxrate 1250k -bufsize 2500k -r 25 -keyint_min 25 -g 50 -sc_threshold 0 -c:a aac -ar 44100 -b:a 96k -ac 2 -pix_fmt yuv420p -movflags + faststart 480.mp4 \
-s 640x360 -c:v h264 -crf 33 -tune film -profile:v main -level:v 4.0 -maxrate 900k -bufsize 1800k -r 25 -keyint_min 25 -g 50 -sc_threshold 0 -c:a aac -ar 44100 -b:a 96k -ac 2 -pix_fmt yuv420p -movflags +faststart 360.mp4 \
-s 320x240 -c:v h264 -crf 36 -tune film -profile:v main -level:v 4.0 -maxrate 625k -bufsize 1250k -r 25 -keyint_min 25 -g 50 -sc_threshold 0 -c:a aac -ar 22050 -b:a 64k -ac 1 -pix_fmt yuv420p -movflags +faststart 240.mp4
```
# Producing DASH Streaming
Dynamic Adaptive Streaming over HTTP.  
Dash is the open-source equivalent of Apple's HLS technology.  
```shell
mp4fragment 1080.mp4 1080-f.mp4
mp4fragment 1080.mp4 1080-f.mp4&&mp4fragment 720.mp4 720-f.mp4&&mp4fragment 480.mp4 480-f.mp4&&mp4fragment 360.mp4 360-f.mp4&&mp4fragment 240.mp4 240-f.mp4
mp4dash 240-f.mp4 360-f.mp4 480-f.mp4 720-f.mp4 1080-f.mp4
```
# Streaming Mp4 Files
MP4 files consist of chunks of data called atoms.  
You can think the moov atom as a kind of table of contents for the MP4 file.  
When streaming,your browser requests the video and starts receiving the beginning of the file. It looks to see if the moov atom is near the start.
```shell
-movflags +faststart
```
# Producing Adaptive WebM DASH Streaming
The DASH technology can be used also to deliver videos in WebM format.  
For streaming WebM files using DASH, the video and audio files have to be non-muxed and non-chunked, meaning that each video stream goes into it's own file and each audio stream goes into it's own file.
# Scaling with FFMPEG
```shell
ffmpeg -i YOUR_INPUT.mov -s 1280x720 YOUR_OUTPUT.mp4
```
# Overlay Images on Pictures
```
ffmpeg -i backgroundimage.jpg -i logoimage.png -filter_complex "overlay=0:0" finaloutput.jpg
```
# Convert Images to WebP
The WebP format is a lossless and lossy compression for images for the web.
```shell
brew install webp
```
```shell
cwebp YOUR_INPUT.png -o YOUR_OUTPUT.webp
dwebp YOUR_INPUT.webp -o YOUR_OUTPUT.png
```
# Batch Convert Pictures from RAW to JPEG format
The RAW format is a proprietary digital camera format that contains all the infomation captured by the camera's sensors.
SIPS = "Scriptable Image Processing System"
```shell
sips -s format jpeg -s formatOptions 80 "IMG_001.cr2" --out "IMG_001.jpg"
```
# Ghostscript for PDF processing
```shell
brew install ghostscript
gs input.pdf output.pdf
```
# Extract Images from PDF
```shell
convert example_input.pdf extracted/img03%d.png
```
# Create Animated Slides from Still Pictures
```shell
ffmpeg -framerate 1 -i img%03d.png -r 25 -pix_fmt yuv420p output.mp4
```
# Extract Images from Video
```shell
ffmpeg -i INPUT -f image2 -bt 20M -vf fps=1/5 %03d.png
```
# Extract Audio from Video
```shell
ffmpeg -i YOUR_VIDEO.mp4 -vn -c:a copy youraudio.aac
```
# How to make a high-quality GIF from a video
1. Download the video  
2. Cut the desired portion, resize teh original video from HD to SD(or less)
3. Create an SRT Subtitle File
4. Save the SRT file
5. Convert the SRT to ASS Format
6. Modify the size and the font as desired
7. Burn the .ass subtitle into the video
8. Create PNG Conversion for GIF Elaboration
9. Make the GIF
```shell
ffmpeg -ss 00:29:18 -i yourvideo.mkv -t 00:00:10 -avoid_negative_ts 1 output.mp4
ffmpeg -i output.mp4 -vf scale=640x360 -c:v h264 -crf 18 -c:a aac outputscaled.mp4
nano mycaptions.srt
ffmpeg -i mycaptions.srt mycaptions.ass
Style: Default,Avenir,20,&H00FFFFFF,&H000000FF,&H80000000,&H80000000,-1,0,0,0,100,100,0,0,4,0,0,2,10,10,10,1
ffmpeg -i putputscaled.mp4 -filter_complex "subtitles=sub.ass" videosubtitled.mp4
ffmpeg -i videosubtitled.mp4 frame%04d.png
gifski -o myvideo.gif frame*.png # high quality
ffmpeg -i videosubtitled.mp4 mygif.gif # low quality
```

# How to add an Overlay Banner and burn subtitles onto a video
1. Create a desired banner
2. Export the graphic in PNG format
3. Overlay the graphic on top of your video.
```shell
ffmpeg -i videoinput.mp4 -i imageinput.png -filter_complex "[0:v][1:v] overlay=0:0:enable='between(t,0,10)'" -pix_fmt yuv420p -c:a copy output.mp4
ffmpeg -i videoinput.mp4 -i graphicheader.png -filter_complex "[0:v][1:v] overlay=0:0,subtitles=sub.ass" -pix_fmt yuv420p -c:a copy output.mp4
```
