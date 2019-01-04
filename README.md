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

# Project3 (stream a stored video using HTTP protocol)

#### *Used OS:* **Ubuntu 18.04.1**

# Pre-requirements:
### Setup a webserver (XAMPP for example)
* download [XAMPP for Linux](https://www.apachefriends.org/index.html)
* open the downloads folder and right-click on the installed file
* click on properties --> permission tab
* click on "Allow executing file as program"
* open a terminal and go to the Downloads folder
* type "ls" commad to see the list of files in the download folder
* copy the installed program name from the result list
* type "sudo ./{paste the copied name}" command and enter your password
* click next on the popped up window and wait to finish installing

### To Launch XAMPP through the Terminal
Run the following commands..
* cd /opt/lampp
* ls
* sudo chmod 755 manager-linux-x64.run 
* sudo ./manager-linux-x64.run 
* test the server using [localhost](http://localhost/dashboard/)

### Change the permissions of the root directory of the server 
Change file owner of /opt/lampp directory. username should be the username of your new documentroot folder ownername
* sudo chown -hR  {your-user-name}:root /opt/lampp
* sudo gedit /opt/lampp/etc/httpd.conf
    - edit the following lines
        ```
         <IfModule unixd_module>
            User nobody
            Group nogroup
         </IfModule>
        ```
* restart xampp


### Setup [MP4Box tool](https://gpac.wp.imt.fr/2015/07/29/gpac-build-mp4box-only-all-platforms/)

### Setup FFmpeg tool
* sudo apt update
* sudo apt install ffmpeg

# The tools: FFmpeg and MP4Box
Two tools will be used. **FFmpeg** to prepare the video content, and **MP4Box** to segment the file and create a Media Presentation Description (MPD).

# Steps 
To complete the project do the following:
1. *Download a video from the internet*
2. *Setup a webserver server and test it using localhost (127.0.0.1)*
3. *Use the MP4Box software tool to produce DASH compatible video stream; manifest (.mpd) and video files (.m4s)  and store these files in the root directory of the webserver*<br>
    **Preparing the video file**
    ```sh
    ffmpeg -i input.mp4 -c:v libx264 -preset slow -x264-params keyint=96:min-keyint=96 output1.264
    ```
    | Parameter | Explanation |
    | ------ | ------ |
    | -preset slow | Presets can be used to easily tell x264 if it should try to be fast to enhance compression/quality. Slow is a good default. |
    | min-keyint=96 | Sets the maximum interval between keyframes. This setting is important as we will later split the video into segments and at the beginning of each segment should be a keyframe. |
    | keyint=96 | Sets the minimum interval between keyframes. We achieve a constant segment length by setting minimum and maximum keyframe interval to the same value |
    | output1.264 | Specifies the output filename. File extension is .264 as it is a raw H.264/AVC stream. |
    
    **Segmenting**<br>
    Now we add the previously created h264 raw video to an mp4 container as this is our container format of choice.
     ```sh
    MP4Box -add output1.264 -fps 24 output2.mp4
    ```
    | Parameter | Explanation |
    | ------ | ------ |
    | output1.264 | The H.264/AVC raw video we want to put in a mp4. |
    | -fps 24 | Specifies the framerate |
    | output2.mp4 | The output file name |
    
    What follows is the step to actual create the segments and the corresponding MPD.
     ```sh
    MP4Box -dash 4000 -frag 4000 -rap -segment-name segment_ output2.mp4
    ```
    | Parameter | Explanation |
    | ------ | ------ |
    | -dash 4000 | Segments the given file into 4000ms chunks. |
    | -frag 4000 | Creates subsegments within segments and the duration therefore must be longer than the duration given to -dash. By setting it to the same value, there will only one subsegment per segment |
    | -rap | Forces segments to start random access points, i.e. keyframes. Segment duration may vary due to where keyframes are in the video |
    | -segment-name segment_ | The name of the segments. An increasing number and the file extension is added automatically. So in this case, the segments will be named like this: segment_1.m4s, segment_2.m4s, … |
    | output2.mp4 | The video we have created just before which should be segmented |
    
    **The output is one video representation, in form of segments. Additionally, there is one initialization segment, called output2.mp4. Finally, there is a MPD**
4. *Create a blank webpage and add the JavaScript player*
     ```
    <script
    src="http://cdn.dashjs.org/latest/dash.all.min.js">
    </script>
    <h4> DASH.js Player Demo </h4>
    <style>
    video {
    width: 640px;
    height: 360px;
    }</style>.
    <div>
    <video data-dashjs-player
    src="output2_dash.mpd" controls></video>
    </div>
     ```
5. *Use any internet browser to view the webpage locally and play the video*
