本文记录了有关阅读TransH《Knowledge Graph Embedding by Translating on Hyperplanes》的相关内容，如果有同学在做相关的工作，可以与我联系zhaoliang19960421@outlook.com
# 背景
TransH 论文是在基于[TransE](https://editor.csdn.net/md/?articleId=106375998)进行改进的，TransE算法中将每个关系认为是在实体空间中对于三元组的翻译，具体意思就是说在向量空间中，关系向量负责将头实体向量**翻译**成尾实体向量，这也是所有的Trans算法的思路。
但是在TransE算法中无法解决自反关系、多对一关系、一对多关系。
	
	一对多关系就是一个头实体和很多个尾实体有用一样的关系。
	例如：周星驰-主演-功夫，周星驰-主演-长江七号
	按照TransE的方法，是利用主演将周星驰翻译到功夫和长江七号上。
	如果存在一个知识图谱使得每个三元组的势能差值都是0，
	那么意味着从周星驰利用关系翻译到功夫的势能差是0
	也意味着从周星驰利用关系翻译到长江七号的势能差也是0
	换句话说，就是功夫和长江七号的向量是一样的。这显然是不对的。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200528175610959.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzIyMjM1MDE3,size_16,color_FFFFFF,t_70)

论文中已经对这个问题解释的很清楚了
![在这里插入图片描述](https://img-blog.csdnimg.cn/202005281743255.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzIyMjM1MDE3,size_16,color_FFFFFF,t_70)
# 思路
为了解决上面的提到的TransE的问题，TransH的做法是在向量空间中给每个关系画出了一个超平面，将头实体或者尾实体的向量在超平面上映射之后，在超平面上利用映射之后的头实体向量和尾实体向量进行翻译。具体的也就是下图所示
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200528175014587.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzIyMjM1MDE3,size_16,color_FFFFFF,t_70)
这么做的结果是对于同样的一对多关系，周星驰-主演-功夫，周星驰-主演-长江七号，功夫和长江七号会在**主演**这关系所在的超平面上是一样的，但是这两个实体在向量空间中可以不一样，那么功夫和长江七号在向量空间中就可以区别开，这样就解决了一对多关系的问题。
# 方法
基于以上的思路，TransH算法需要对实体做一个向量，对关系做两个向量（一个超平面的法向量来表示超平面，一个在超平面上的翻译向量），然后对每个正例三元组的头尾实体在超平面上做投影，得到投影之后的向量。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200528175916399.png)

其中W就是关系的超平面法向量，法向量的模长必须是1.这个映射公式也很好理解。
头实体向量$h$,头实体在关系超平面上的投影向量是$h^r$，关系的超平面的法向量是w，关系在超平面上的翻译向量是d，那么投影的向量和实体向量和法向量的关系就是下图所示的
![在这里插入图片描述](https://img-blog.csdnimg.cn/2020052818123678.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzIyMjM1MDE3,size_16,color_FFFFFF,t_70)
$h=h^r+xw$,我们的目的就是求出这个X，那么就可以利用向量差来求出我们要的这个$h^r=h-xw$。而且$h·w=|w||r|·sin(\theta)=x$
那么我们要求的$h^r=h-xw=h-h·w·w$

现在我们已经得到了头尾实体在关系超平面上的投影向量，那么利用这个投影向量在超平面上进行翻译就可以了，在超平面上的翻译和TransE算法是一样的。
# 损失函数
TransE算法的损失函数采用的就是正负例的势能差值，目的是使得正例三元组的势能差值尽量小，使得负例的势能差值尽量大，两个样本之间的差距最大是margin就可以了，比这个差距还要大并没有更多的奖励。
但是在TransH的l损失函数中增加了叫软约束的条件，利用这个方法约束了三个条件	
- 关系法向量模长必须是1
- 实体向量模长必须是1 
- 关系法向量和关系翻译向量必须正交（翻译向量在法向量所对应的超平面上）
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200529100531699.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzIyMjM1MDE3,size_16,color_FFFFFF,t_70)
具体的损失函数的定义方法是
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200529100624564.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzIyMjM1MDE3,size_16,color_FFFFFF,t_70)

利用两个超参数来控制软约束的能力。

	但是在很多开源的代码中的损失函数并没有软约束的，都是按照TransE写的，
	发邮件给作者也没有回复，如果有同学手里有TransH的原生代码
	或者知道其他的开源作者这么做的理由给的
	还麻烦您邮件和我联系 zhaoliang19960421@outlook.com
	
