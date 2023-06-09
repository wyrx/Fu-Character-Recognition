# Fu-Character-Recognition 福字识别

This repository is used to store our ML final homework, a model to identify Fu character. 

## 总览

本模型使用了两种特征共同判断输入的一张图片是否是汉字“福”，第一种特征[^1]是汉字的外轮廓特征和内轮廓特征，两者并在一起一共80维，第二种特征是分别计算了输入图片的sift、surf、orb、shi-tomasi特征的视觉词袋嵌入，每种特征5维，一共有100维的图片特征。

采用了两种分类器进行分类，第一种是adaboost分类器，网格调参后，在验证集上f1-macro可以达到`0.925215723873442`，在测试集上能够准确的分出所有图片。第二种是单分类模型OneClassSVM，训练集为全体福字，在训练集上调参至最优后，在测试集上f1-macro可以达到`0.8000000000000002`，说明能够较为准确地区分出福字与非福字。

为了比较两个分类器的泛化性能，额外准备了10张图片作为额外测试集，对于非福字特意选取了与训练集极为不同的图片，包括一张与“福”字极为相似的“楅”字，甚至包括二维码以及风景等非文字内容。发现adaboost分类器在额外测试集上f1只有`0.4000000000000001`，而单分类模型仍能有`0.5833333333333333`的f1，说明单分类模型在福字识别上具备更强的泛化性。

[^1]:https://blog.csdn.net/jy02660221/article/details/52012543

## 使用

首先更改`preprocessing.ipynb`文件中的输入输出路径，完成对图片的预处理。

随后直接运行`model.ipynb`中的代码即可完成分类。

## 安装需求

由于使用了专利保护的surf算法提取特征，需要使用下列命令（linux）安装opencv-contrib-python，或者直接安装低版本opencv：

`CMAKE_ARGS="-DOPENCV_ENABLE_NONFREE=ON" pip install --no-binary=opencv-contrib-python opencv-contrib-python`

## 分工

1. 数据采集：负责从各个来源（如网络、字体库等）收集不同字体的福字样本。由一个人负责。

2. 数据预处理：负责对采集到的数据进行预处理，如图片格式转换、尺寸统一、去除噪声等。由一个人负责。

3. 特征提取：负责从预处理后的数据中提取有效的特征，如颜色、纹理、形状、笔画等特征。由一个人负责。

4. 特征选择与降维：负责对提取的特征进行选择和降维，有可能需要选择特征后再进行特征融合，以减少特征数量和提高分类效果。由一个人负责。

5. 模型建立与训练：负责选择合适的分类算法，建立分类模型并进行训练。由一个人负责。

6. 模型评估与调优：负责对训练好的分类模型进行评估和调优，以提高分类效果。由一个人负责。

## 备注

### 数据采集需要注意

尽可能多采集不同字体、形态等等的福字图片。尽可能不要包含不是福字的内容，参考老师测试集的样例。同时注意也要采集一些**非福字**的汉字图片，用于测试模型效果。

### 数据预处理注意：

将搜集到的图片转化成500*500像素的黑白numpy数组，以与测试集格式相匹配。注意许多图片可能携带水印，从彩色图片转化可能包含噪声，记得去除噪声。

### 提取特征方法可以参考：

HOG特征：HOG（Histogram of Oriented Gradients）是一种用于目标检测和行人识别的特征提取方法。它通过计算图像中局部方向梯度直方图来描述图像纹理和形状信息。

SIFT特征：SIFT（Scale-Invariant Feature Transform）是一种用于图像匹配和目标跟踪的特征提取方法。它通过检测和描述图像中局部特征点的关键点和描述子来实现。

SURF特征：SURF（Speeded Up Robust Features）是一种与SIFT类似的特征提取方法，但更加快速和鲁棒。它通过计算图像中特征点的尺度和方向来描述局部特征。

ORB特征：ORB（Oriented FAST and Rotated BRIEF）是一种快速的、旋转不变的特征提取方法。它将FAST关键点检测器和BRIEF描述子组合在一起，同时引入旋转不变性。

Harris角点特征：Harris角点检测是一种用于图像特征点提取的方法，它可以检测出具有较高变化率的像素点，即角点。在OpenCV中，可以使用cv2.cornerHarris函数来实现Harris角点检测。

Shi-Tomasi角点特征：Shi-Tomasi角点检测是一种改进的Harris角点检测方法，它在保持角点检测准确度的同时，更加稳定和鲁棒。在OpenCV中，可以使用cv2.goodFeaturesToTrack函数来实现Shi-Tomasi角点检测。

### 特征选择注意：

上一步提取到特征后需要选择有用的特征。

常见特征选择方法：

相关性分析法：该方法通过计算每个特征与类别之间的相关性，选取与类别高度相关的特征。常用的相关性分析方法包括Pearson相关系数、Spearman相关系数和Kendall相关系数等。

方差分析法：该方法通过比较不同特征的方差来选取具有区分能力的特征。通常选择方差较大的特征作为最终特征集。

互信息法：该方法通过计算每个特征与类别之间的互信息来选取最具有区分能力的特征。互信息是信息论中的一个概念，它衡量了两个变量之间的相关性。

Wrapper方法：该方法利用分类器对特征子集进行训练和评估，从而选择具有最佳分类性能的特征子集。Wrapper方法通常包括前向搜索和后向搜索两种。

Embedded方法：该方法是将特征选择和分类器训练结合在一起进行优化，常用的Embedded方法有基于Lasso的特征选择方法和基于树模型的特征选择方法等。

### 分类模型可以参考：

推荐使用单分类模型，从而避免数据不均衡的问题。

Support Vector Data Descriptor(SVDD)，其它方法可以参考：`Perera P, Oza P, Patel V M. One-class classification: A survey[J]. arXiv preprint arXiv:2101.03064, 2021.`
