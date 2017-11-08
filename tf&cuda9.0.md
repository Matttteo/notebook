

由于目前TensorFlow的pip安装版本还不支持cuda9.0（见这个[issue](https://github.com/tensorflow/tensorflow/issues/12052)），因此需要从源码安装。

## 前提
CUDA 9.0
CuDNN7.0.3

## 安装过程
###1. 下载TensorFlow源码
###2. 切换到最新版本1.4
```
git clone https://github.com/tensorflow/tensorflow
cd tensorflow
git checkout r1.4
```
###3. 安装bazel
```
sudo add-apt-repository ppa:webupd8team/java
sudo apt-get update && sudo apt-get install oracle-java8-installer
echo "deb [arch=amd64] http://storage.googleapis.com/bazel-apt stable jdk1.8" | sudo tee /etc/apt/sources.list.d/bazel.list
curl https://bazel.build/bazel-release.pub.gpg | sudo apt-key add -
sudo apt-get update && sudo apt-get install bazel
sudo /sbin/ldconfig -v
```
###4. configure
```
.configure
```
这个过程中设置cuda和cudnn的版本
###5. 编译TensorFlow
```
bazel build --config=opt --config=cuda //tensorflow/tools/pip_package:build_pip_package --cxxopt="-D_GLIBCXX_USE_CXX11_ABI=0"
bazel-bin/tensorflow/tools/pip_package/build_pip_package /tmp/tensorflow_pkg
pip install /tmp/tensorflow_pkg/tensorflow-1.4.0-cp36-cp36m-linux_x86_64.whl
```
这里遇到的一个坑
```
ERROR: /home/devops/codebase/tensorflow/tensorflow/core/kernels/BUILD:5995:1: no such package '@sqlite_archive//': Error downloading [http://www.sqlite.org/2017/sqlite-amalgamation-3200000.zip]
```
这是因为sqlite自己的URL变了，修改```tensorflow/tensorflow/workspace.bzl```中的```
sqlite_archive```为 ```https://www.sqlite.org/2017/sqlite-amalgamation-3200000.zip```

###6. 一个小坑
import TensorFlow的时候出现错误
```
No module named tensorflow.python.platform
```
解决办法，不要在TensorFlow源文件目录下进入python

### 参考：
http://www.52nlp.cn/%E6%B7%B1%E5%BA%A6%E5%AD%A6%E4%B9%A0%E6%9C%8D%E5%8A%A1%E5%99%A8%E7%8E%AF%E5%A2%83%E9%85%8D%E7%BD%AE-ubuntu17-04-nvidia-gtx-1080-cuda-9-0-cudnn-7-0-tensorflow-1-3

http://www.jianshu.com/p/f459f4ab0d99