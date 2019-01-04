# What is FFmpeg?
FFmpeg is the leading multimedia framework, able to decode, encode, transcode, mux, demux, stream, filter and play pretty much anything that humans and machines have created.
Download from [here](https://www.ffmpeg.org/download.html) 

# FFmpeg Tools

  - [ffmpeg](https://ffmpeg.org/ffmpeg.html): is a command-line tool to convert multimedia files between formats
  - [ffprobe](https://ffmpeg.org/ffprobe.html): a simple multimedia stream analyzer
  - [ffplay](https://ffmpeg.org/ffplay.html): a simple media player based on SDL and the FFmpeg libraries

# FFmpeg Libraries for developers 
* [libavutil](https://ffmpeg.org/libavutil.html) is a library containing functions for simplifying programming, including random number generators, data structures, mathematics routines, core multimedia utilities, and much more.
* [libavcodec](https://ffmpeg.org/libavcodec.html) is a library containing decoders and encoders for audio/video codecs.
* [libavformat](https://ffmpeg.org/libavformat.html) is a library containing demuxers and muxers for multimedia container formats.
* [libavdevice](https://ffmpeg.org/libavdevice.html) is a library containing input and output devices for grabbing from and rendering to many common multimedia input/output software frameworks, including Video4Linux, Video4Linux2, VfW, and ALSA.
* [libavfilter](https://ffmpeg.org/libavfilter.html) is a library containing media filters.
* [libswscale](https://ffmpeg.org/libswscale.html) is a library performing highly optimized image scaling and color space/pixel format conversion operations.
* [libswresample](https://ffmpeg.org/libswresample.html) is a library performing highly optimized audio resampling, rematrixing and sample format conversion operations.

# Project1 (transcode videos into different formats using various encoding parametters)
## Steps
1. Download a video from the Internet with duration less than 10 minutes, or could download a longer video but use [ffmpeg to cut it to less than 10 minutes](https://stackoverflow.com/questions/18444194/cutting-the-videos-based-on-start-and-end-time-using-ffmpeg)
2. use ffmpeg to transcode the video with different QP values and measure:
    1. Bit rate of the output file
    2. PSNR of the output file as compared to the original file
3. Repeat point 2 for many QP values and then plot the resulted rate distortion curve (curve fitting the <rate,PSNR> points).

 ##### Transcoding with QP
```sh
ffmpeg -i input.mp4 -qp <int> output.mp4
```
`<int>` is a number from 0 to 63
QP "quantizaer" is a number, the smaller the number the higher in the quality, the higher the quality the higher the file size (i.e, 0 is the highest [quality lossless compression] and 63 is the lowest quality)

 ##### Bit rate finding
* Find the output video file size (S) (in bits)
* Find the video duration (T) (in seconds)
* Calculate: Bit rate = S/T


 ##### PSNR finding
 ```sh
ffmpeg -i input.mp4 -i output.mp4 -filter_complex "psnr" -f null –
```
Read more about [PSNR](https://www.ffmpeg.org/ffmpeg-all.html#psnr) 

# Project2 (transcode videos with specific target bit rate in H.264 and H.265 codec)
## Steps
1. Use ffmpeg to transcode the video with specific target bit rate in H.264 and H.265 codec and measure: 
* Bit rate of the output file
    ```sh
    ffmpeg -i input.mp4 -c:v libx264 -b:v <bitrate> ouput.mp4
    ```
    ```sh
    ffmpeg -i input.mp4 -c:v libx265 -b:v <bitrate> ouput.mp4
    ```
    **<bitrate> is a variable to be specified**
* PSNR of the output file as compared to the original file 
     ```sh
    ffmpeg -i input.mp4 -i output.mp4 -filter_complex "psnr" -f null –
    ```
2. Plot the resulted two rate distortion curve by fitting the measured values for each of the codecs. 

