官方文档见[delf.md](https://github.com/weihongwei3/DELF/blob/master/delf.md)

下为Windows环境下从github拿到代码并且调试运行成功的过程详解，理解错误之处欢迎指正。

# 一、说明
操作系统：win10（Linux教程可见官方说明，此为Windows环境下的操作）

编译器：pycharm

python版本：3.6.8（anaconda环境）

TensorFlow版本：1.13.1

>项目地址：[TensorFlow](https://github.com/tensorflow/models)  || [DELF](https://github.com/tensorflow/models/tree/master/research/delf)

>ps:学好英语很重要

>ps:科学上网很重要

>初入深度学习，可能有解释不正确的地方，欢迎指正

---
---

# 二、DELF 安装
## Tensorflow
CPU版本和GPU版本安装一个即可，GPU版本的话请确认自己的[显卡版本](https://tensorflow.google.cn/install/gpu)是否支持
```
# CPU版本:
pip install tensorflow
# GPU版本:
pip install tensorflow-gpu
```
## Protobuf
DELF库使用protobuf（python版本）来配置特征提取及其格式。
protobuf应该在安装TensorFlow的时候就跟着安装了，如果没有，就
```
pip install protobuf
#需要更新最新版本的话
#pip install protobuf --upgrade
#需要卸载的话
#pip uninstall protobuf
```

同时您将需要 protoc编译器，版本> = 3.3。最简单的方法是直接下载。
对于Linux：
```
wget https://github.com/google/protobuf/releases/download/v3.3.0/protoc-3.3.0-linux-x86_64.zip
unzip protoc-3.3.0-linux-x86_64.zip
PATH_TO_PROTOC=`pwd`
```
对于Windows：

下载proto编译器

[protoc-3.7.1-win64.zip](https://github.com/protocolbuffers/protobuf/releases/download/v3.7.1/protoc-3.7.1-win64.zip)

[protoc-3.7.1-win32.zip](https://github.com/protocolbuffers/protobuf/releases/download/v3.7.1/protoc-3.7.1-win32.zip)

添加bin目录绝对路径至环境变量

![在这里插入图片描述](https://img-blog.csdnimg.cn/20190413171038325.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDg5NDk3OA==,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190413171110570.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDg5NDk3OA==,size_16,color_FFFFFF,t_70)

安装protocol编译器成功后，检测

![sa ](https://img-blog.csdnimg.cn/20190413175309720.png)

表示安装成功
注：protoc编译器容易因为版本出现众多问题，后续出现问题可降为3.4.0
## Python dependencies
安装python库依赖：
```
pip install matplotlib
pip install numpy
pip install scikit-image
pip install scipy
```
## tensorflow/models
现在，克隆tensorflow/models并安装所需的库:(注：object_detection库需要手动添加tensorflow/models/research/到PYTHONPATH库中，详细过程在安装完tensorflow/models后讲解）
```
git clone https://github.com/tensorflow/models
```
>关于git速度极慢的问题，不知是因为小文件数量太多，还是因为访问github的网络问题
>- 尝试过修改DNS缓存，无效，可自行搜索尝试
>- 尝试用SVN，显示小文件数量太多，一个个加载速度很慢，放弃
>- 尝试打包ZIP下载，下载速度10kib/s，放弃，所以好像又是访问速度问题？？？？
> - 最后的解决方案是，在我的VPS上git clone，速度大概30MB/S，可怕，然后用FTP工具（我用的filezilla）下载到本地

首先，安装 slim's "nets" package.
```
#进入 models/research/slim/ 文件夹
pip install -e .
```
第二，通过编辑PYTHONPATH设置object_detection模块：

新建PYTHONPATH环境变量，添加 tensorflow/models/research/  文件夹的绝对路径

然后，编译DELF的protobufs
```
#进入 tensorflow/models/research/delf/  文件夹
protoc delf/protos/*.proto --python_out=.
```
正常应该是不输出东西，如果编译出错，可能是proto版本的问题，我用3.7.1最新版本无法编译，最后下载[3.4.0版本](https://github.com/protocolbuffers/protobuf/releases/download/v3.4.0/protoc-3.4.0-win32.zip)编译完之后，再重新安装3.7.1在项目中使用（因为TensorFlow 1.13.1要求protobuf包大于3.5，而包和编译器要版本对应）

最后，安装DELF包。
```
# 进入 tensorflow/models/research/delf/  文件夹
# Install "delf" package.
pip install -e . 
```
此时
```
import delf
```
不报错表示安装成功

## object_detection库


Dependencies
Tensorflow Object Detection API depends on the following libraries:

- Protobuf 3.0.0
- Python-tk
- Pillow 1.0
- lxml
- tf Slim (which is included in the "tensorflow/models/research/" checkout)
- Jupyter notebook
- Matplotlib
- Tensorflow (>=1.12.0)
- Cython
- contextlib2
- cocoapi

1. 安装TensorFlow，之前已安装
2. 其他依赖库
```
pip install --user Cython
pip install --user contextlib2
pip install --user pillow
pip install --user lxml
pip install --user jupyter
pip install --user matplotlib
```

3. Protobuf编译
Tensorflow对象检测API使用Protobufs配置模型和训练参数。在使用框架之前，必须编译Protobuf库。这应该通过从tensorflow / models / research /目录运行以下命令来完成：
```
# 进入 tensorflow/models/research/  文件夹
protoc object_detection/protos/*.proto --python_out=.
```
注意：如果在编译时遇到错误，则可能使用的是不兼容的protobuf编译器。如果是这种情况，请使用手动安装3.4.0版本

4. 将库添加到PYTHONPATH
在本地运行时，tensorflow/models/research/ 和 slim目录应该附加到PYTHONPATH。

之前已经把tensorflow/models/research/ 目录的绝对路径添加进PYTHONPATH环境变量，现在再添加tensorflow/models/research/slim

![在这里插入图片描述](https://img-blog.csdnimg.cn/20190413194424242.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDg5NDk3OA==,size_16,color_FFFFFF,t_70)

5. 测试
您可以通过运行以下命令来测试您是否已正确安装Tensorflow Object Detection API：
```
python object_detection/builders/model_builder_test.py
```
---
---
**分割线**


到这里是配好了DELF模型的环境，代码在本地还是跑不了的，example文件夹是示例代码，还需再添加训练好的模型

---
---
# 三、代码概述
DELF的代码位于delf目录下。其中有两个目录，protos和python。

**delf/protos**
该目录包含三个protobufs：
- datum.proto：用于序列化浮动张量的通用protobuf。
- feature.proto：protobuf用于序列化DELF功能。
- delf_config.proto：protobuf用于配置DELF提取。

 **delf/python**
此目录包含用于多种不同目的的文件：
- datum_io.py，feature_io.py是用于读取和写入张量和特征的辅助文件。
- delf_v1.py 包含创建DELF模型的代码。
- feature_extractor.py包含使用DELF提取功能的代码。这对于提取多个尺度的特征，基于注意力得分的关键点选择以及PCA /白化后处理特别有用。

除此之外，此目录中的其他文件包含不同模块的测试。

该子目录delf/python/examples包含运行DELF特征提取和匹配的示例脚本：
- extract_features.py 从图像列表中启用DELF提取。
- match_images.py支持使用提取的DELF功能进行图像匹配extract_features.py。
- delf_config_example.pbtxt 显示了用于DELF特征提取的DelfConfig原型的示例实例化。


---
---

# 四、Quick start: DELF extraction and matching
## dateset&DELF model

1. 下载[数据集](http://www.robots.ox.ac.uk/~vgg/data/oxbuildings/oxbuild_images.tgz)

在tensorflow/models/research/delf/delf/python/examples/下新建data文件夹，进入data文件夹，再在data文件夹里新建oxford5k_images 和 oxford5k_features文件夹，把数据集解压到oxford5k_images里，以上是官方介绍。

其实就是下载了一堆相关照片，选两张来测试。在examples下建一个文件夹test_images，放两张图片，我选的all_souls_000006.jpg和all_souls_000013.jpg。

![在这里插入图片描述](https://img-blog.csdnimg.cn/20190413183956617.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDg5NDk3OA==,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/2019041318401217.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDg5NDk3OA==,size_16,color_FFFFFF,t_70)

新建一个list_images.txt放两张图片的URL，添加URL

可以采用命令行方式：
```
# 进入  tensorflow/models/research/delf/delf/python/examples/ 文件夹
echo test_images/image_1.jpg >> list_images.txt
echo test_images/image_2.jpg >> list_images.txt
```
也可以直接在list_images.txt写两行：
```
test_images/image_1.jpg
test_images/image_2.jpg
```
在博客“[图像检索中的DELF模型（DEep Local Features）实践](https://blog.csdn.net/sparkexpert/article/details/80590452)”中博主用了两个https链接，但是我用的时候会异常退出，最终是用了两个本地图，解决。

2.  此外，还需要下载训练好的的DELF模型：

下载[模型]( http://download.tensorflow.org/models/delf_v1_20171026.tar.gz)
进入tensorflow/models/research/delf/delf/python/examples/文件夹，新建parameters文件夹并将模型压缩包解压到其中

## DELF feature extraction
pycharm里打开项目，项目结构：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20190413183710528.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDg5NDk3OA==,size_16,color_FFFFFF,t_70)

运行extract_features.py，提取两张图片的特征，生成两个“.delf”文件存储特征

![在这里插入图片描述](https://img-blog.csdnimg.cn/20190413183331296.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190413183315821.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190413183542290.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDg5NDk3OA==,size_16,color_FFFFFF,t_70)

## Image matching using DELF features
提取特征后，运行match_images.py匹配两张图的特征点

![在这里插入图片描述](https://img-blog.csdnimg.cn/20190413183904441.png)

并生成了匹配图 matched_images.png

![在这里插入图片描述](https://img-blog.csdnimg.cn/20190413184058189.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDg5NDk3OA==,size_16,color_FFFFFF,t_70)

---
---

# 五、BUG
初入深度学习，踩坑无数，由于未做及时记录，有些排除了就忘记了，想起来再随时更新吧

1.object_detection/protos/*.proto: No such file or directory
使用proto编译，如果报错，很可能就是版本问题，目前最新版3.7.1编译时会报no such directory，解决方法就是降版本，用3.4.0版本的编译器可以成功编译。**但是**！编译完以后，再换回3.7.1版本，因为项目中使用TensorFlow要用高版本，高版本TensorFlow要求protobuf包高于3.5版本，最好就用最新的，然后proto编译器要和protobuf包的版本一致，不然项目运行时会报错。不知道自己理解的对不对，总之最后是采用这种奇葩的方式解决了问题。
**换proto编译器版本的时候别忘了改环境变量并保存，再新开cmd编译**

2.Your CPU supports instructions that this TensorFlow binary was not compiled to use: AVX AVX2
表示你的机器CPU可以支持，但是目前的TensorFlow不支持，可以忽略，反正能跑，如果要解决，就是下源码自行编译，详情自行百度。

3.MetaGraphDef associated with tags 'serve' could not be found in SavedModel.To inspect available tag-sets in...
please use the SavedModel CLI: `saved_model_cli_'
没下载DELF模型的时候出的错误，重新查看步骤，肯定有漏的

4.Cannot parse file b'parameters/delf_v1_20171026/model/saved_model.pb': Error
没下载模型

5.Process finished with exit code -1073741819 (0xC0000005)
两张图片URL用了https，改用本地图片解决。如果你是其它项目和问题遇到这个错误码，
见[pycharm报错：Process finished with exit code -1073741819 (0xC0000005)](https://blog.csdn.net/qiao1025566574/article/details/81037908)，差不多可能的情况全了
