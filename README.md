# Anti-fraud


未完待续。。。

蚂蚁金服风险大脑 支付风险识别比赛
网址：https://dc.cloud.alipay.com/index#/topic/intro?id=4

# 成绩
1. 初赛成绩
   * a榜最高：0.3919
   * b榜最高：0.3807（后面和一个大佬融合一下达到了0.3993，以下的思路只涉及到我的模型）
2. 复赛成绩
   * a榜（2018-8-12 0.3528）排名 53/（800+）

# 数据介绍
https://dc.cloud.alipay.com/index#/topic/data?id=4
1. 初赛数据：
297个字段（没有真实含义，命名都为f1,f2,f3...f297)+ date+id
大多数都是离散变量，只有个别为连续变量
数据体量：训练数据：994731条 测试数据约为一半
模糊样本数:4725
正负样本比例：12122：977884约为1:80

2. 复赛数据
33个有具体含义的特征（比如证件省、市，IP省市，双方的ID，收款方的银行卡号、手机号等）
数据体量：训练数据：条  测试数据：条
正负样本比例：
模糊样本数:

# 我的思路：
直接利用所有数据，并不作处理

# 具体细节：

### 缺失值填充方面：

### 处理模糊样本方面：
官方给出的介绍有提到过，模糊样本的来源在于他们之前的系统砍掉的交易，因为他们认为这些交易有风险，所以砍掉。
我尝试过对模糊样本进行k-means聚类，聚成两类，但发现4725个样本中只有6条被判出来是无风险的，所以并没有什么用。。。
对模糊样本其实还可以这样处理：再得到一些白样本的pattern之后，用规则等方式判别出好的样本，从而减少模糊样本的数量

### 在特征选择方面
我是采用了随机森林来选去特征的，300个中发现选出175个是最好的，我还尝试过PCA筛选特征，效果并不好，第一个主成分占到了93%+

### 样本非常不平衡方面：
我尝试过上采样，下采样，smote，smote+enn等多种方式，在我这个模型中都比不上不采样的效果好
在这里我的思考是：其实处理过后auc是有所增加的，但是这里的评估方式其实是更注重概率的准确性，
他们是希望在保证识别一定的风险下更注重对好交易的判断，不想砍掉一些好交易来降低用户体验。
在不平衡的数据处理方面，推荐一个包： imbalanced-learn（这里面有多种采样方式的api
http://contrib.scikit-learn.org/imbalanced-learn/stable/index.html

### 在模型集成方面：
集成DNN: 我尝试过用xgboost的叶子结点进行编码，一开始我的想法是对xgb生成的300个节点进行01编码（具体说来就是对于每棵树，
加入有30个叶子节点，对其中样本落入的叶子结点置为1，其余的为0）然后把这个放入到网络中，但这个产生的编码实在是太稀疏了。。
可能是因为这个原因导致网络的效果实在是不好
除此之外，和同学讨论，他觉得这个输入并没有意义，所以我又思考了一下，了解了xgb在损失函数为逻辑回归的时候是如何计算概率的：
分享一下当时学习的帖子：https://www.cnblogs.com/harekizgel/p/7683803.html
所以我采取了叶子的分数，而不是简单的0，1
但这样的网络效果依然不好，甚至还有倒退的趋势。。。不知为何，
问了一个老哥，老哥说这个网络调参还是需要技术的。。。难受。。

### 在使用规则方面：


### 在特征工程方面：
特征工程主要是在复赛进行，因为复赛还未结束，这里先不分享～

### 在参数方面：
我的调参过程是这样的：我采用的是gridsearch进行调参
附上我的调参教程：

 
一些新的思考：
官方给出的一些想法非常值得我慢慢评为




