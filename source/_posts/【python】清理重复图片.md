---
title: 【python】清理重复图片
date: 2021-09-02 12:41:18
tags:
---
### 引言

本文解释如何编写python脚本，实现重复图片清理的功能。

### 正文
MD5,Message-Digest Algorithm，信息摘要算法，是一种信息加密算法，可以把任意长度的数据转换为一个长度固定的数据串（通常是16进制的字符串表示）。
一个图片文本的本质是一个数据串，所以要想清理重复图片，就是要匹配到相同的数据串的两张图片，使用DM5的目的是将图片的数据串统一，如果两张图片经过MD5算法变换后的数据串相同，
则可判断这两种图片重复。

以计算一个字符串的MD5值为例，代码如下：
```
import hashlib

md5 = hashlib.md5()
md5.update('how to use md5 in python hashlib?'.encode('utf-8'))
print(md5.hexdigest())
```
计算结果为
```
d26a53750bc40b38b65a520292f69306
```
参考网站：[廖雪峰老师博客中摘要算法简介](https://www.liaoxuefeng.com/wiki/1016959663602400/1017686752491744)

本文的脚本的思路如下：
1 遍历文件夹下的图片文件
2 把图片文件记录到一个字典变量中
3 生成所有图片文件的MD5值
4 把MD5值存放到一个新的字典变量中
5 判断如果已经存放，则该图片为重复图片，删除即可

## 选取文件夹，遍历文件夹下所有的文件
```
inpath = "G:/最新/照片/2018-2019年照片备份/2021年"

for path,dir,filelist in os.walk(inpath):
    for filename in filelist:
        # print(filename)
        allfile.append(os.path.join(path,filename))
```
## 获取MD5值
```
    fd = open(file,'rb')
    md5 = hashlib.md5()
    md5.update(fd.read())
    fd.close()
    return md5.hexdigest()

```
## 根据MD5值比较
```
for photo in allfile:
    md5sum = getmd5(photo)
    if md5sum not in md5list:
        md5list.append(md5sum)
    else:
        identicallist.append(photo)
```
## 删除重复照片
```
for idenPhoto in identicallist:
    os.remove(idenPhoto)
```	

### 总结
完整代码放在了[Github](https://github.com/janice143/photoClear)上，如果读者有兴趣，不妨试一试。
