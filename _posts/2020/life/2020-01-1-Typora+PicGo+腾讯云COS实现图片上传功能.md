---
layout: post
title:  Typora+PicGo+腾讯云COS实现图片上传功能
category: life
copyright: life
no-post-nav: true
tags: [life]
excerpt: Typora

---

简单的实现在Mac环境使用Typora+PicGo+腾讯云COS实现图片上传功能。



<!--more-->

### 1. 安装Typora和PicGo

- **Typora安装**

因为在较新版本的Typora中（在MacOS上为0.9.9.32或在Windows / Linux上为0.9.84），才有“上传图像”功能，这样才可以通过第三方应用程序或脚本将图像上传到云图像存储，所以Typora必须升级到比较新的版本（`我这里是版本0.9.9.33.2 (4442)`）。安装过程较为简单，此处省略。

- PicGo安装

安装也比较简单，安装地址：https://molunerfinn.com/PicGo/



### 2. 在COS中创建bucket并配置秘钥

![创建bucket](https://funnylu-1259196254.cos.ap-beijing.myqcloud.com/java/image-20201122161222112.png)



### 3. 配置PicGo上传服务

![配置PicGo](https://funnylu-1259196254.cos.ap-beijing.myqcloud.com/java/image-20201122163013670.png)



### 4. 关联Typora与PicGo

- 打开Typora 文件->偏好设置

![image-20201122163331406](https://funnylu-1259196254.cos.ap-beijing.myqcloud.com/java/image-20201122163331406.png)

![image-20201122163404355](https://funnylu-1259196254.cos.ap-beijing.myqcloud.com/java/image-20201122163404355.png)



成功！