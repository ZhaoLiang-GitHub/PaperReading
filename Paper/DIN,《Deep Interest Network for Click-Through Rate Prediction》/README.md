本文记录了阅读DIN《Deep Interest Network for Click-Through Rate Prediction》的论文阅读笔记，如果有做相关工作的同学可以邮件和我联系zhaoliang19960421@outlook.com
# 背景
## 常见CTR模型
1. 大规模稀疏特征映射到低维embedding向量
2. multi-hot特征多个Embedding向量pooling成group-wise定长向量，group-wise定长concatenate
3. MLP学习特征间非线性关系

## 现有模型中存在的问题
1. 用户的多种兴趣固定表征在定长的用户向量中限制了embedding类模型的表达能力
2. 计算某一个广告点击率时不需要将用户的全部兴趣压缩进一个向量里，因为只有部分兴趣影响此次点击率的预估

# 思路
为了解决这个问题，将不同的行为分组，每个组内表达了用户的一种行为习惯，通过contact将一个组的行为向量变成了一个。对于一个特定的广告而言，得到广告的向量，以此和不同组的行为向量计算得到一个权重，然后可利用改权重将所有组的向量按权求和得到最终的向量在通过全链接得到最终的预测CTR。
在论文中将这种计算特定的广告和不同组的行为向量之间相关的模块称之为local activation unit.
个人感觉这种做法和attention类似，计算得到的不同的行为组特征和给定广告之间的相关性。
# 工程加速
DIN模型实际上就是在现有的MLP模型的基础上增加了一个激活模块。
但是对于工程的影响重要的是两个工程加速
## Mini-batch Aware Regularization
在大规模的稀疏系数的L2正则化
## Data Adaptive Activation Function
一种更加灵活的激活函数。





