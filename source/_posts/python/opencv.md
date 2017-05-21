title: opencv
date: 2014-12-11 10:46:22
updated: 2014-12-11 10:46:30
tags: 
- python
categories: 
- 图像处理
---

###安装配置
>1. easy_install numpy
>2. sourceforge上下载 opencv,解压，将cv2.pyd复制到python 的sitepackage目录，然后(很容易疏忽)把opencv_ffmpeg2410.dll复制到系统path路径包含的任意目录，注意这里的文件名加上了opencv版本好（2.4.10）否则打开任意的视频文件都有问题(False)

###使用
##取帧

    :::python linenos='table' linenums=True
    cap = cv2.VideoCapture(r'D:\PFS_Data\2014-11-21 11-49-43@jobKey-34.avi')
    print cap.isOpened()
    data = cap.read()
    #save to disk
    cv2.imwrite(r'E:\hello.jpg',data[1])
    
    
