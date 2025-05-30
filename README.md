# DST-NET
本项目基于语义分割网络，使用中分辨率时序遥感影像提取农田地块的空间分布情况。农田地块相较于一般的分割对象，有一定的时序信息，因此本项目基于论文《Semantic Segmentation Based on Temporal  Features: Learning of Temporal–Spatial  Information From Time-Series SAR  Images for Paddy Rice Mapping》（IEEE TGRS SCI 1区TOP，2022）中的TFBS网络进行复现与改造，提取目标物从水稻更改为农田。针对这一提取目标，本项目针对该网络的具体改造如下:  
★ 使用 <font color=DC143C>ConvLSTM</font> 模块替换原文中的LSTM模块。  
原文使用学习LSTM学习水稻的季相节律信息，本项目使用ConvLSTM学习农田的季相节律信息。ConvLSTM在每个时间步使用卷积运算来处理输入数据，因此可以更好地捕捉图像数据中的空间结构，避免NODATA值影响。  
★ 使用<font color=DC143C>Res-U-net</font>基础网络替换原文中的U-net。  
Res-U-Net的优势在于通过残差连接解决了深层网络中的梯度消失问题，虽然原文网络结构并不深，但是使用残差链接可以使得网络在训练过程中更加稳定，能够更好地捕捉复杂的特征。  
★ 添加了<font color=DC143C>基于空洞可分离卷积的空间注意力机制</font>。  
这种机制可以提升模型感受野和对地块形状的感知能力。  
★ 将模型的损失函数更换为<font color=DC143C>focal loss</font>。  
多篇论文指出，该损失函数可以有效提升对不平衡样本的学习能力。  

原文链接：https://ieeexplore.ieee.org/document/9506988  
原文代码：https://github.com/younglimpo/TFBSmodel

# 模型架构
![模型架构](modelflow/模型架构.png)

# 数据处理流程（样本处理流程）
1. 从谷歌地球工程平台(GEE)下载时序哨兵2号遥感影像（2021年4月-9月，每月使用中值合成一幅影像，研究区：内蒙古鄂托克前旗）,并进行重投影工作（UTM 50N）;（基于java实现）
2. 使用代码data_merge.py对获取的影像进行波段融合；
3. 在arcgis中制作渔网划分训练集与验证集矢量;
4. 先使用固定窗口（data_mask.py）裁剪，再使用滑动窗口裁剪(data_clip.py);
5. 使用data_aug.py对训练集和验证集进行样本扩增；

# 人员分工
吴尚岐（2024303110137）：项目提出、总体框架搭建、模型修改与调试、空间注意力机制搭建  
宋祝蓓佳（2024303110112）：模型调试与损失函数测试  
吴佳佩（2024303110110）：数据下载与数据重投影、波段融合（基于python实现）  
张梦楚（2024303110113）：训练集与验证集划分与裁剪（基于python实现）  

# 模型环境
## 硬件环境：
13th Gen Intel(R) Core(TM) i7-13700K   3.40 GHz  
RAM 64.0 GB  
Windows 10 x64  
NVIDIA GeForce GTX 1080 Ti  
## 软件环境：
Python==3.8.18  
tensorflow==2.4.0  
GDAL==2.3.3（遥感影像处理包）  
rasterio==1.3.11（栅格数据处理包） 

# 不同文件夹
data_demo给出了一些模型的预测结果，由于文件大小限制，我们无法上传完整区域的预测结果  
dataprocess给出了一些数据的处理流程  
model_result给出了模型的训练精度结果  
model中是模型的训练、预测以及精度评估代码

# 写在最后
我们的项目改造了现有的论文网络模型，将适用于提取水稻分布的网络模型改造为适用于提取特定形状的耕地地块的网络模型，充分发挥了深度学习语义分割网络对于空间特征的挖掘和长短期记忆网络对于时序特征的学习能力，基于该方法提取得到的农田地块的空间分布情况，可以为后续的农田变化提供精确的数据支撑。
