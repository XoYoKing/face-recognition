# face-recognition
基于FaceNet Model的面部识别程序

本项目是我在coursera上实现的一个项目的源代码，图文介绍及核心算法参见[这个页面]（https://github.com/zhuangzheng/face-recognition/edit/master/face-recognition.ipynb）
数据集来自face.net

下面简单介绍一个这个项目
------------------------------------------------------------------------------------------------------------------------------------------

项目实现了两个功能，第一个是身份验证，即扫描目标的脸，将其与数据库中相同姓名的面孔比较，核实是否同一个人，是一个1:1问题

第二个是面部识别，即扫描目标的脸，识别目标是否在面孔数据库中，是一个1：K问题（效果参见https://www.youtube.com/watch?v=wr4rx0Spihs）


在facenet模型中，面部图片使用一个卷积神经网络重新编码为一个128-dimensional的向量，这个向量充分提取了该图片的特征，提供了远比原图片更高的精确度

这一部分的inception块的实现在文件incenption_block.py中，参考原文 https://arxiv.org/abs/1409.4842

这部分将（m,3,96,96）的图片输入转化(m,128)的输出

     

在训练时，模型的损失函数采用triplet loss,即用（A,P,N）图像对，A表示Anchor为一张面孔，Positive表示与Anchor同一人的脸，Negative表示另一个人的脸，

当两张脸属于同一人时，我们希望L2（A，P）尽可能小，当两者不同时，即L2（A，N）尽可能大，于是定义L=max(0,L2(A,P)-L2(A,N)+alpha),alpha是一个

hyperparameter，项目中设为0.2,通过调整alpha,可以改变识别程序的精确度

训练完成后，便可以进行身份验证和面部识别，两者使用的是同一个训练好的模型：
    用于身份验证的时候，比较目标和对应人的面部图片，在编码后向量的L2距离，小于一定值即为同一人（本项目中使用的阈值为0.7），否则输出False
    用于面部识别的时候，比较目标和数据库中所有的面孔的L2距离，寻找差值最小者，如果小于阈值，则判断为同一人，否则输出False
