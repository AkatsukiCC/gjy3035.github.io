---
layout: post
category: "env"
title:  "编译caffe遇到的问题汇总"
tags: [CAFFE]
description:  "编译caffe遇到的问题汇总"
---
# 2016.1.13更新

## 问题描述

在编译caffe最后一步：```make runtest -j40``` 时，会出现如下错误：

{% highlight python linenos %}
$ make runtest -j40
.build_release/tools/caffe
.build_release/tools/caffe: /usr/lib64/libstdc++.so.6: version `GLIBCXX_3.4.15' not found (required by /home/anothergjy/Desktop/caffe-master-matlab-python-gpu02-251/
.build_release/tools/../lib/libcaffe.so)
.build_release/tools/caffe: /usr/lib64/libstdc++.so.6: version `GLIBCXX_3.4.15' not found (required by /usr/local/lib/libopencv_highgui.so.2.4)
make: *** [runtest] Error 1
{% endhighlight %}

原因是版本太低。需要更高的版本。关于版本的查看可以用下面的命令：
{% highlight python linenos %}
strings libstdc++.so.6.0.18| strings /usr/lib64/libstdc++.so.6|grep GLIBCXX
{% endhighlight %}
## 解决办法

这里已经安装好了GCC-4.8.2，因此需要修改软连接即可。

需要连接的文件存在于```gcc-build-4.8.2/x86_64-unknown-linux-gnu/libstdc++-v3/src/.libs/libstdc++.so.6.0.18```下面。
但需要注意的是src目录下的.lib文件夹是隐藏的，需要使用root权限才可以看到。
接下来，就是要修改连接，让原本的libstdc++.so.6指向ibstdc++.so.6.0.18。
{% highlight python linenos %}
# cd /usr/lib64
# rm -r libstdc++.so.6
rm: remove symbolic link `libstdc++.so.6'? y
# ln -s libstdc++.so.6.0.18 libstdc++.so.6
{% endhighlight %}

查看版本结果如下：
{% highlight python linenos %}
# strings libstdc++.so.6.0.18| strings /usr/lib64/libstdc++.so.6|grep GLIBCXX
GLIBCXX_3.4
GLIBCXX_3.4.1
GLIBCXX_3.4.2
GLIBCXX_3.4.3
GLIBCXX_3.4.4
GLIBCXX_3.4.5
GLIBCXX_3.4.6
GLIBCXX_3.4.7
GLIBCXX_3.4.8
GLIBCXX_3.4.9
GLIBCXX_3.4.10
GLIBCXX_3.4.11
GLIBCXX_3.4.12
GLIBCXX_3.4.13
GLIBCXX_3.4.14
GLIBCXX_3.4.15
GLIBCXX_3.4.16
GLIBCXX_3.4.17
GLIBCXX_3.4.18
GLIBCXX_3.4.19
GLIBCXX_FORCE_NEW
GLIBCXX_DEBUG_MESSAGE_LENGTH
{% endhighlight %}

可以看到最新版本已经到了3.4.19。

# 2016.1.11更新

## 问题描述

由于最新版的caffe支持多卡，因此，在编译过程中，如果检测到机器是多卡，便会在runtest阶段出现报错：
{% highlight python linenos %}
	The difference between XXX and XXX...
{% endhighlight %}

## 解决方法
对于这样的问题，只需要加入一行代码，```export CUDA_VISIBLE_DEVICES=0```

这样以来，runtest就可以顺利通过了。










