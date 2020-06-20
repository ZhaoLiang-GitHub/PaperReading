本文时 KG2E，《Learning to represent knowledge graphs with gaussian embedding》的论文阅读笔记，如果有做相关工作的同学可以与我联系 zhaoliang19960421@outlook.com

# 背景
之前的TransX系列的论文都是在欧式空间中对实体和关系进行表征，然后利用基于空间中点相似度的计算方法来计算势能（通过关系翻译的头实体和真实尾实体之间的差距）

![](https://note.youdao.com/yws/public/resource/f97529f58c24ed5763a838a3bf7976d1/xmlnote/C42C0825CC8746DC98FDF1168B8BEC66/3914)

在论文中提出了之前的Trans系列论文没有关注到的点，在本文中称之为实体/关系的准确性（按照论文中的说法，实际上和[TranSparse](https://github.com/ZhaoLiang-GitHub/PaperReading/tree/master/GraphEmbedding/TransX/TranSparse%EF%BC%8CAAAI2016%EF%BC%8C%E3%80%8AKnowledge%20graph%20completion%20with%20adaptive%20sparse%20transfer%20matrix%E3%80%8B) 提出了异构性、平衡性是一个意思，KG2E发表在CKIM2015,TranSparse发表在AAAI2016）

![1](https://note.youdao.com/yws/public/resource/f97529f58c24ed5763a838a3bf7976d1/xmlnote/94D070D8D1DD4E36B3CD6FCBEF81068D/3916)
论文中有关于影响到准确性的点和TranSparse中的异构性和平衡性的定义是一样的。

对于实体的准确性高低的影响定义成了，一个实体/关系的准确性会影响到在三元组中确定性的置信度，论文中的举例就是在三元组 希拉里-配偶-克林顿，希拉里-国家是-美国，这两个三元组里，配偶这个关系在确定这个三元组中的置信度更好，因为通过配偶可以直接从克林顿到希拉里，但是通过国家是不能直接从美国到希拉里。这么说在这个三元组中配偶关系的确定性更高。

![1](https://note.youdao.com/yws/public/resource/f97529f58c24ed5763a838a3bf7976d1/xmlnote/BDEADC105F604D3EB9AEBAF72ED5ABD5/3918)

所以KG2E将这个问题考虑了进来，在高斯空间中对实体和关系进行表征，高斯空间里面的均值（也就是高斯空间中的概率最高点的位置）作为表征的结果。

用这个高斯空间的协方差矩阵（在实际计算中使用的协方差矩阵的对角向量）来表示准确性，使用协方差矩阵的意义在于通过计算不同特征之间的协方差来表示特征之间的关系，协方差矩阵（对角向量）之间相似度越大，那么在三元组中的相关性也就越大，置信度也就越高，准确度也就越高。

    这个是我的个人理解，没有在论文上找到关于协方差和准确度的证明，如果您知道更好更准确的解释，麻烦您通过邮件和我沟通联系 zhaoliang19960421@outlook.com

#  思路
基于以上的背景知识，KG2E没有在欧式空间中而是在高纬高斯空间中进行了计算，高纬高斯的均值作为position（也就是最终的结果）

和之前的Trans系列鲁文一样，也是计算的势能差值，最小化了全局的合叶损失函数。因为实体/关系是在高纬空间中进行表征的，无法利用欧式空间中的点之间的计算方法，所以在本文中计算从头实体通过关系映射得到向量和标准尾实体之间的势能差值采用的概率之间的计算方法。

论文中的基础定义符号如下图

![1](https://note.youdao.com/yws/public/resource/f97529f58c24ed5763a838a3bf7976d1/xmlnote/B2C57160CCDB44F48858651830100EE8/3920)

对于每个头实体、尾实体、关系都是个高斯分布。

那么利用翻译的功能，即将头实体通过关系翻译得到的备选实体也是个高斯分布，那么就需要计算的这个备选的实体高斯分布和真实的尾实体高斯分布之间的距离。

论文中提出了两种计算方法，对称的势能差损失，非对称的势能差

- 非对称的势能差损失函数

    非对称的方法利用的是KL散度的计算方法

    先得到备选实体的分布$P_e$
    ![1](https://note.youdao.com/yws/public/resource/f97529f58c24ed5763a838a3bf7976d1/xmlnote/D296132EE19B401691BECABF6FC5D2FB/3922)
    然后利用KL算法计算备选实体和标准尾实体之间的距离

    ![1](https://note.youdao.com/yws/public/resource/f97529f58c24ed5763a838a3bf7976d1/xmlnote/7FAB3429A7C34F33B0DF54678AF3AC64/3924)
    由于KL散度是非对称的，那么这种势能差的计算方法也是非对称的，也就是$\varepsilon(h,r,t)\neq\varepsilon(t,r,h)$

    然后利用该损失函数，通过SGD来进行参数更新，具体的参数计算结果如下图

    ![1](https://note.youdao.com/yws/public/resource/f97529f58c24ed5763a838a3bf7976d1/xmlnote/C4234D1E8AD94796B4D4F59A7C5EEA96/3926)

- 对称的势能差损失函数

    在论文中提到还尝试了另外一个对称的损失函数，也就是
$\varepsilon(h,r,t)+\varepsilon(t,r,h)$，把正反两个方向的KL散度相加，但是实验结果并没有提升，所以没有使用

    对称的方法实际上就是将两个概率（一个将头实体通过关系进行翻译的备选实体和标准尾实体）的分布想乘做积分

    ![1](https://note.youdao.com/yws/public/resource/f97529f58c24ed5763a838a3bf7976d1/xmlnote/A34D41FAE504478F9004CC372A637D5E/3928)

    具体的求梯度方法如下图所示

    ![1](https://note.youdao.com/yws/public/resource/f97529f58c24ed5763a838a3bf7976d1/xmlnote/FC0D81899311421F97957727E977A4AD/3930)

# 伪代码
KG2E 的伪代码如下图所示

![1](https://note.youdao.com/yws/public/resource/f97529f58c24ed5763a838a3bf7976d1/xmlnote/C6B9C1AF2F5B4E4FB2D2C44A18D05357/3932)  

和之前的算法类似，通过随机初始化的方法对于每个实体和关系初始化一个高斯分布，其中高斯分布的均值是随机初始化的，高斯分布的协方差矩阵是通过两个超参数来控制的上下限。

然后进行对正样本采样，催正样本采样后生成负样本，负样本的生成方式有两种一种是随机改变头尾实体，一种是改变关系。

然后将正样本+两个负样本进行计算。

计算方法需要提前确定是对称的还是非对称的。

然后在进行梯度计算修改参数。

在每一轮的计算中都需要对均值和协方差矩阵进行规范化，

# 代码

在原始论文中没有给出初始代码，我在网络上也没有找到KG2E的代码（python语言，TF、pytorch框架)，如果您曾经对这篇论文采用进行了复现或者您手里有合适的代码，如果可以分享的话，麻烦您邮件和我沟通联系 zhaoliang19960421@outlook.com ，在此提前对您表示感谢！

