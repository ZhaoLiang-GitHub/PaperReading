本文记录了《PLATO-2- Towards Building an Open-Domain Chatbot via Curriculum Learning》的阅读笔记，如果有同学在做相关的工作可以邮件和我联系 zhaoliang19960421@outlook.com

# 背景
用更大的模型和更多的数据，构建一个高质量的对话模型，实现在对话任务中的获得更高质量的回复。

# 模型
该论文将对话任务拆分成了两个部分。在两个部分一共构建了三个模型。

第一个任务是生成一个一对一回复的生成模型，具体意义就是说利用该生成模型从context中生成一个response。

第二个任务是对第一个生成模型进行微调，生成一个一对多的生成模型，具体意思是从contest中获得好几个response。然后在训练一个评估模型，评估一对多模型生成的句子的质量，然后挑选一个质量高的句子作为当前的回复。

# 特点
这篇论文采用的还是TransFromer架构的模型，采用的是两阶段法的模型，先训练一个粗的一对一的生成模型，然后在微调一个静的一对多模型，然后在挑一个。
这三个模型都是利用的TransFromer模型，具体模型如下图 

![1](https://note.youdao.com/yws/public/resource/f97529f58c24ed5763a838a3bf7976d1/xmlnote/05C457A2AC614DC798944D9ECE88AF7D/3934)