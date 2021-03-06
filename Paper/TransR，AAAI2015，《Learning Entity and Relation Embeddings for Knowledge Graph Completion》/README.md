本文记录了阅读TransR论文时的相关内容，如果有做相关工作的同学，欢迎与我沟通联系 zhaoliang19960421@outlook.com

# 背景
TransR论文的提出还是为了来解决在TransE中无法解决的自反、一对多、多对一的关系问题。
有一个很显然的想法就是，在知识图谱中的实体和关系是不一样，那么他们存在的语义空间也就应该不一样，实体空间就是专门的来表示实体上的属性，关系空间就是来表示实体之间的关系内容的。
TransR就是利用这样的想法，对于实体和关系分别构建了一个空间（空间维度可以一样，也就是说当两个空间维度一样的时候实际上和TransE是一样的）。在计算三元组的势能差时，由于实体和关系不在同一个空间中，无法直接的计算，需要用一种映射方法将实体映射到对应的关系空间里面，然后利用在关系空间里面的实体的映射向量进行计算。
# 思路
在论文中的图像也清楚的显示TransR 和TransE的区别
![1](https://note.youdao.com/yws/public/resource/f97529f58c24ed5763a838a3bf7976d1/xmlnote/120F888EB63549B090691E898914A037/3886)
对于每一个关系，都有一个映射矩阵，这个矩阵负责将实体（包括头实体和尾实体）从实体空间映射到关系空间，然后在关系空间中进行计算（论文中写的是在关系空间中进行TransE）
论文上的图片在原始的实体空间中的对于每一类实体（头实体、尾实体）中都是聚集在一起的（三角形和圆形）；但是通过映射之后，和这个关系有关的实体被聚集其它的被远离（三角形都远离了圆形，而圆形是具有这个关系的)。从图上可以看出来，通过对某个特定关系翻译矩阵的翻译功能，可以将在本来在实体空间中无法区分的实体，有了很好的区分，有了很好的区分的结果就是最终的编码效果会更好。
# 方法
![1](https://note.youdao.com/yws/public/resource/f97529f58c24ed5763a838a3bf7976d1/xmlnote/6F2ED00D184B4F31A7252FC78F24DFDC/3888)
具体的操作方法在原始的论文中可以看到，就是对于每个关系创建了一个映射矩阵，将实体映射到了关系空间之后就和普通的TransE一样了。
# 改进
在论文中又提出了一种对于TRansR的改进方法，称为CTransR，其中C是cluster聚类的意思。
具体而言，就是在进行CTransR 之前，先要进行一次初始化（论文中采用的TransE），已经得到了最初版本的向量编码结果，利用这个最初的向量编码结果来进行聚类，将同一个关系下的实体聚类到了不同的团，那么一个关系向量就被聚类成了好几个向量，不同的聚类团分别使用自己聚类团的关系向量。
那么损失函数也发生了变化，原来只是需要计算下通过特定关系的映射矩阵翻译过来的实体在关系空间中的TransE的势能差，变成了还需要计算聚类团的关系向量和标准关系向量的距离。标准关系向量也是通过初始化方法获得的
![1](https://note.youdao.com/yws/public/resource/f97529f58c24ed5763a838a3bf7976d1/xmlnote/610CC5E9B7DD4E828D974418A8FD91D0/3890)
论文中的$r_c$就是不同的聚类团的关系向量，$r$就是这个关系的标准向量，$M_r$就是这个关系的映射矩阵。
在CTransR中，虽然每个先将实体进行聚类之后有了不同的聚类团关系向量，但是每个关系的映射矩阵还是只有一个，这也就能说明，虽然通过聚类的方法将关系用不同的向量来表示，但是在大体上还是一样的，因为用的是一个映射矩阵，当映射矩阵都不一样了，那么就是彻底不一样了。
# 求助
我查看了很多网上的链接，但是都没有找到论文的原生代码，如果您恰好手中又TransR/CTransR的代码，如果可以分享的话，麻烦您通过邮件（zhaoliang19960421@outlook.com）和我联系，在此提前向您表示感谢！