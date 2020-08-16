文本记录了TransG《TransG : A Generative Model for Knowledge Graph Embedding》的阅读笔记，如果有做相关工作的同学可以邮件和我沟通联系  zhaoliang19960421@outlook.com

# 背景
在知识图谱中存在一个关系有多个含义的问题，在论文中举例是《琼恩雪诺，出生，临冬城》《奥巴马，出生，夏威夷》，那么「出生」这个相同的关系，实际上有不同的的含义，第一个三元组中是出生、虚拟人物出生地，第二个三元组是出生、真实人物出生地。这两个三元组中相同的一个关系却有两个不同的含义。
![1](https://note.youdao.com/yws/public/resource/f97529f58c24ed5763a838a3bf7976d1/xmlnote/24C7F360AD764F0D997417A7CAB2C3A9/3986)
在论文中给出的解释有两个原因
    
    1. 在构建知识图谱时会把相似的关系合并成为一个统一的关系
    2. 在所有的语言和知识中都有 歧义的内容
![1](https://note.youdao.com/yws/public/resource/f97529f58c24ed5763a838a3bf7976d1/xmlnote/4357A4F185DC40F1A713C2EE5231807D/3988)

而当前的基于翻译的图表征模型中是将每一个关系都表征成一个向量，每个关系一个向量就无法解决一个关系还有多个语义的问题。

为了解决这个问题，TransG的思路和CTransR的思路类似，就是给同一个关系不同的关系「类别」，不同的类别使用不同的关系向量。

和CTransR不同的是，CTransR中同一个关系的不同向量的个数是一个超参数需要人工指定，在TransG中不同类别的个数是可以通过学习得到的。而且，TransG中关系和实体向量空间也不是之前的欧式空间，而是在概率分布空间。

在论文中将这种做法称之为，贝叶斯的无参数无限生成模型
![1](https://note.youdao.com/yws/public/resource/f97529f58c24ed5763a838a3bf7976d1/xmlnote/E6385F6F1CDE458388B11792AA379C4D/3993)

# 思路

1. 对于每个实体，新建一个服从N(0,1)的正太分布
2. 

