# webrtc 支持.264下载编译
几个注意点：
（1）需要安装Windows 10 SDK(10.0.16299.15)这个工具
（2）需要安装VS2017，尽量将C++相关工具安装完整，并按照默认路径安装
（3）需要将操作系统 “当前系统区域”设置为“英语（美国）”

1、下载depot_tools
配置下载工具代理环境变量：
set http_proxy=http://127.0.0.1:1080
set https_proxy=https://127.0.0.1:1080

下载完成depot_tools.zip后解压到D:\webrtc\depot_tools中，然后并加入环境变量PATH
使用管理员权限打开cmd，执行gclient sync，下载特定版本的git和python

2、下载webrtc代码
新建目录D:\webrtc\webrtc-checkout，然后使用管理员权限打开cmd，进入D:\webrtc\webrtc-checkout目录，
配置下载工具git环境变量：
git config –global http.proxy http://127.0.0.1:1080 
git config –global https.proxy https://127.0.0.1:1080 

设置BOTO代理，解决download google storage失败问题（似乎不能解决问题）：
set NO_AUTH_BOTO_CONFIG=D:\webrtc\depot_tools\http_proxy.boto
[Boto] 
proxy= 127.0.0.1 
proxy_port= 1080 

设置不再次下载工具链：
set DEPOT_TOOLS_WIN_TOOLCHAIN=0

执行fetch webrtc，开始下载代码，中途如果断网，可以执行gclient sync继续下载。


3、生成工程项目
设置下面的环境变量：
set DEPOT_TOOLS_WIN_TOOLCHAIN=0
set GYP_GENERATORS=msvs-ninja,ninja 
set GYP_MSVS_VERSION=2017
gn gen out/h264Debug --ide=vs2017 --args="target_os=\"win\" target_cpu=\"x64\" proprietary_codecs=true rtc_use_h264=true ffmpeg_branding=\"Chrome\""

修改third_party/chromium/config/chromium/linux/x64/config.h文件，将#define CONFIG_H264_DECODER 0修改为1
其中，proprietary_codecs表示要启用H264 codec。上述执行完毕，即在src目录下生成VS2017工程文件。用VS2017打开all.sln即可进行编译。

codec 的顺序调整在InternalEncoderFactory的构造函数里。
