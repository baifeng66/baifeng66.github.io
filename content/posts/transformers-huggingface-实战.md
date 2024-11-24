+++
date = '2024-11-23T22:11:54+08:00'
title = 'Transformers-Huggingface-实战'
categories = ["Python"]
tags = ["自然语言处理", "NLP"]
+++
[手把手带你实战HuggingFace Transformers-视频教程](https://www.bilibili.com/video/BV1ma4y1g791/)

# 基础组件

## Pipeline-管道

- 是将`数据预处理`、`模型调用`、`结果后处理`三部分组装成的流水线

### 步骤流程图

![image-20241123222047897](https://raw.githubusercontent.com/baifeng66/photos/master/img/image-20241123222047897.png)

### 创建与使用方式

```python
from transformers import pipeline
```

- 根据任务类型直接创建管道

```python
pipe = pipeline("text-classification")
```

- 指定任务类型，再指定模型，创建基于指定模型的管道（指定GPU/MPS加速）

```python
pipe = pipeline("text-classification", model="uer/roberta-base-finetuned-dianping-chinese",device=0/"mps")
```

- 预加载模型，再创建管道

```python
from transformers import AutoModelForSequenceClassification, AutoTokenizer

# 这种方式，必须同时指定model和tokenizer
model = AutoModelForSequenceClassification.from_pretrained("uer/roberta-base-finetuned-dianping-chinese")
tokenizer = AutoTokenizer.from_pretrained("uer/roberta-base-finetuned-dianping-chinese")
pipe = pipeline("text-classification", model=model, tokenizer=tokenizer)
```

## Tokenizer-分词器

- **主要功能**是将输入的自然语言文本转换为模型可以理解的数字化形式

![image-20241123223517501](https://raw.githubusercontent.com/baifeng66/photos/master/img/image-20241123223517501.png)

### 基本使用

```python
from transformers import AutoTokenizer
sen = "弱小的我也有大梦想!"
```

- 加载保存

```python
# 从HuggingFace加载，输入模型名称，即可加载对于的分词器
tokenizer = AutoTokenizer.from_pretrained("uer/roberta-base-finetuned-dianping-chinese")
# tokenizer 保存到本地
tokenizer.save_pretrained("./roberta_tokenizer")
```

- 句子分词

- 查看词典
- 索引转换
- 填充截断
- 其他输入

上述5个功能由一个`tokenizer`即可解决~

```python
inputs = tokenizer(sen, padding="max_length", max_length=15)
inputs
```

输出结果：

```python
{
'input_ids': [101, 2483, 2207, 4638, 2769, 738, 3300, 1920, 3457, 2682, 106, 102, 0, 0, 0], 
 'token_type_ids': [0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0], 
 'attention_mask': [1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 0, 0, 0]
}
```

- `input_ids` 是输入文本被 Tokenizer 转换后的 Token ID 序列，每个数字对应一个词或子词在词表（vocabulary）中的索引。
  - 101: 特殊标记 **[CLS]**，表示句子的起始符，模型需要它来表示句子的上下文。
  - 102: 特殊标记 **[SEP]**，表示句子的结束符。
  - 106: 对应句子中的感叹号 “!”。
  - 0, 0, 0: 填充符（Padding），用于对齐输入序列的长度，保证所有输入样本的长度一致。
- `token_type_ids `表示每个 Token 属于哪个句子，用于区分不同句子（尤其是在句子对任务中，如自然语言推理任务）。
  - 如果输入只有一个句子，那么所有的 token_type_ids 都是 **0**，表示该 Token 属于第一个句子。
  - 在句子对任务中（如问答任务），第一个句子对应的 token_type_ids 是 0，第二个句子对应的 token_type_ids 是 1。
  - 这里的例子是单句子，因此全为 0。
- `attention_mask` 表示哪些 Token 是有效的，哪些是填充符（Padding）。
  - **1** 表示有效的 Token。
  - **0** 表示无效的填充部分。

## Model-模型

```python
from transformers import AutoConfig, AutoModel, AutoTokenizer
```

### 载入和保存模型

- 可在线加载

```python
# force_download 是否强制下载 hfl/rbt3 代表载入的模型名称
model = AutoModel.from_pretrained("hfl/rbt3", force_download=False)
```

- 也可clone到本地再载入
  - 方式一：[HuggingFace](https://huggingface.co/models)
  - 方式二：[HuggingFaceMirror](https://hf-mirror.com/models)

```python
!git clone https://hf-mirror.com/hfl/rbt3
# 载入本地模型
model = AutoModel.from_pretrained("rbt3")
```

### 模型

#### 模型类型

现在大部分的预训练语言模型（PLMs）都是基于Transformer block的堆叠，堆叠的方式有三种：

1. 基于Encoder，适合文本分类、命名实体识别、抽取式问答任务等；
2. 基于Decoder，适合文本生成任务；
3. 结合Encoder和Decoder，适合总结摘要、翻译、生成式问答任务等。

![image-20241124101513121](https://raw.githubusercontent.com/baifeng66/photos/master/img/image-20241124101513121.png)

####  Model Head

对于model head，官网给出的解释是将高维向量映射映射到不同的维度上。我觉得可以理解为模型在output前经过的最后一层，通常为1或多个全连接层，作用是将模型的编码结果根据不同类型的任务映射成不同类型的内容。

<img src="https://raw.githubusercontent.com/baifeng66/photos/master/img/image-20241124101724543.png" alt="image-20241124101724543" style="zoom:50%;" />

调用模型本身只会返回一个基于这个模型的编码结果（hidden states)，所以需要model head来当任务头。不同的model head对应不同的任务，有很多种选择：

- *Model（检索隐藏状态）
- *ForCausalLM
- *ForMaskedLM
- *ForMultipleChoice
- *ForQuestionAnswering
- *ForSequenceClassification
- *ForTokenClassification

#### 模型加载参数

加载模型后，可以看到模型本身的一些参数，可以对模型进行配置。

```python
model.config
```

<img src="https://raw.githubusercontent.com/baifeng66/photos/master/img/image-20241124102030881.png" alt="image-20241124102030881" style="zoom:50%;" />

除此之外，还可以在训练过程中修改模型的一些参数，这里要调用`AutoConfig`。

```python
config = AutoConfig.from_pretrained("./rbt3/")
config
```

打印出来的结果和之前一样，但是输入config.的时候会出来很多选项，都是可以设置的参数。

<img src="https://raw.githubusercontent.com/baifeng66/photos/master/img/image-20241124102425824.png" alt="image-20241124102425824" style="zoom:50%;" />

这个模型是一个bert模型，所以可以进入BertConfig中去看更加具体的参数。而BertConfig又继承自PretrainConfig类，里面的参数同样可以进行配置。

```python
from transformers import BertConfig
```

以设置参数`output_attentions`为例，如果调用模型时没有将它设置为True，可以看到模型会输出隐藏状态、池化结果等，但最后的attention都是none。

```python
model = AutoModel.from_pretrained("rbt3")
output = model(**inputs)
output
```

<img src="https://raw.githubusercontent.com/baifeng66/photos/master/img/image-20241124103145416.png" alt="image-20241124103145416" style="zoom:50%;" />

如果设置参数`output_attentions=True`，则会输出attention。

```python
model = AutoModel.from_pretrained("rbt3", output_attentions=True)
output = model(**inputs)
output
```

<img src="https://raw.githubusercontent.com/baifeng66/photos/master/img/image-20241124103256844.png" alt="image-20241124103256844" style="zoom:50%;" />

#### 模型调用

这里的`return_tensors="pt"`是指定tokenizer返回pytorch tensor形式的数据。

```python
sen = "弱小的我也有大梦想！"
tokenizer = AutoTokenizer.from_pretrained("rbt3")
inputs = tokenizer(sen, return_tensors="pt")
```

<img src="https://raw.githubusercontent.com/baifeng66/photos/master/img/image-20241124103353287.png" alt="image-20241124103353287" style="zoom:50%;" />

##### 无Model Head的模型调用

`AutoModel`不带model head编码，模型实际上是对输入的序列进行编码，输出的编码结果就是`last_hidden_state`。

```PYTHON
model = AutoModel.from_pretrained("rbt3", output_attentions=True)
output = model(**inputs)
```

可以看到last_hidden_state的维度是1x*12*x768，其中1是batch的维度（只有一个句子），12是这个句子中token的个数，也就是`"input_ids"`的长度。

##### 有Model Head的模型调用

`AutoModelForSequenceClassification`就是一个带model head编码的模型，它会将last_hidden_state从高维度向量映射成指定的低维向量，如这里指定`num_label=10`，即十分类任务，就为映射成十维向量。

```python
from transformers import AutoModelForSequenceClassification, BertForSequenceClassification

clz_model = AutoModelForSequenceClassification.from_pretrained("rbt3", num_labels=10)
output = clz_model(**inputs)
```

![image-20241124103721158](https://raw.githubusercontent.com/baifeng66/photos/master/img/image-20241124103721158.png)

至于能够通过修改`num_label`就能修改映射维度，是因为这个模型是继承自Bert文本分类模型的，里面定义了将`hidden_size`映射成`num_label`的维度，所以可以这么做。

<img src="https://raw.githubusercontent.com/baifeng66/photos/master/img/image-20241124103954776.png" alt="image-20241124103954776" style="zoom:50%;" />

深入看下这个bert文本分类模型的细节，可以发现他就是三层连在一起，即bert层、dropout层和一个全连接层。

<img src="https://raw.githubusercontent.com/baifeng66/photos/master/img/image-20241124104052497.png" alt="image-20241124104052497" style="zoom:50%;" />

模型的input先经过forward函数，得到output，再将output中的池化结果取出来，也就是[CLS]的向量结果。这是因为在分类任务中，[CLS]是整个序列的类别标签，用于表示该序列所属类。在训练过程中，[CLS]充当预测目标，Bert通过预测[CLS]的类别来学习整个序列的语义信息，并将该类别作为整个序列的预测结果。

<img src="https://raw.githubusercontent.com/baifeng66/photos/master/img/image-20241124104220907.png" alt="image-20241124104220907" style="zoom:50%;" />

而后模型会对我们有没有输入label进行判断，进而判断这是回归任务、二分类任务还是多分类任务，并且根据不同的任务类型去计算loss。最后还指定了返回值的格式，如果没有指定以字典形式返回，则以默认方式返回。

<img src="https://raw.githubusercontent.com/baifeng66/photos/master/img/image-20241124104251438.png" alt="image-20241124104251438" style="zoom:50%;" />





### Transformer

`Transformer`是一个用于NLP序列到序列（seq2seq）任务的模型架构，创新的引入了自注意力机制，在处理序列数据时表现出色。 它主要由以下两个部分组成：Encoder和Decoder，整体结构如下图。

<img src="https://raw.githubusercontent.com/baifeng66/photos/master/img/image-20241124085556610.png" alt="image-20241124085556610" style="zoom:50%;" />

- **Encoder**：红色部分；**Decoder**：蓝色部分。

- Input

  (BPE+PE)：输入由两部分组成，分别是BPE向量和位置向量。

  1. [**BPE**](https://blog.csdn.net/qq_41020633/article/details/123622667)(Byte Pair Encoding)：分词算法，通过将之前没见过的单词切分成见过的subword，从而表达更多的单词，再通过embedding获得向量表示。
  2. **PE**(Positional Encoding)： 在原有的embedding上加上一个位置向量，以区分不同位置的相同单词。

  <img src="https://raw.githubusercontent.com/baifeng66/photos/master/img/image-20241124085818989.png" alt="image-20241124085818989" style="zoom:50%;" />

  - **Model**：Encoder和Decoder块的堆叠。Nx是堆叠的Transformer block的数量，论文中Nx=6。
  - **Onput**(Liner+Softmax)：输出在词表上的概率分布。
  - **Loss function**：标准交叉熵函数。

  总的来说，Transformer的工作流程如下：

  1. 输入的文本进行分词后，每个词会获得一个向量表示即embedding，这个embedding由BPE和PE相加得到。因此，一句话的向量表示时词向量的堆叠，即一个向量矩阵。
  2. 将得到的表示向量矩阵传入Encoder中，经过六个Encoder后得到输入句子的编码矩阵 X。
  3. 编码矩阵 X 会进入Decoder中，Decoder会根据已输入的 i 个词依次预测第 i+1个词。为了防止模型看到后面的词来预测前面的输入，会采取mask操作盖住 i+1 后的词。

####  Encoder

Encoder部分包含三个部分：

1. 多头注意力层（`Multi-Head Attention`）
2. 前馈神经网络（`Feed Forward`）
3. 残差连接（红线部分，即`Add`操作）和正则化层（`Norm`操作）

<img src="https://raw.githubusercontent.com/baifeng66/photos/master/img/image-20241124090937530.png" alt="image-20241124090937530" style="zoom:50%;" />

##### Muti-Head Attention：多头注意力机制

`Muti-Head Attention`是由多个Self-Attention组成的，所以先着重关注Self-Attention的内部结构。Self-Attention希望每个token自主选择应该关注这句话中的哪些token，并进行信息的整合。

###### Masked self-attention：掩码自注意力机制

<img src="https://raw.githubusercontent.com/baifeng66/photos/master/img/image-20241124093536711.png" alt="image-20241124093536711" style="zoom:50%;" />

可以看到，这一部分接收的输入是 Q,K,V 三个矩阵，这部分的计算方式如下：
$$
\rm Attention(\it Q,K,V)=\rm softmax(\it  QK^T)V.
$$
其中， Q,K,V 是由输入 X 与三个不同的权重矩阵 $W^Q,W^K,W^V$相乘的结果，本质上都是 X的线性变换。

> 设 X 是 m×n 的矩阵， $W^Q,W^K,W^V$ 是 n×k 的矩阵，最后得到的 Q,K,V 则是m×k的矩阵。
>
> - Q$K^T$ 相乘后得到m×m的矩阵；
> - 经过softmax层后得到注意力分数的分布，矩阵维度仍是 m×m；
> - 注意力分数与V相乘，还原成维度为 m×k 的矩阵输出，与输入的X一一对应。

<img src="https://raw.githubusercontent.com/baifeng66/photos/master/img/image-20241124095346408.png" alt="image-20241124095346408" style="zoom:50%;" />

总结来说，整个Attention计算过程就是，矩阵 Q,K 相乘得到注意力分数，通过scale系数对规模进行放缩，再通过softmax将注意力分数变成一个概率分布，最后与对应的矩阵 V 进行矩阵乘法，实现对矩阵 V 的加权平均。

详细计算过程可以看这篇博客：[一文搞定自注意力机制](https://blog.csdn.net/weixin_42110638/article/details/134016569)。

> 在这里要先和RNN里的注意力机制做一下区分：
>
> - RNN的注意力机制：给定一个 query 向量和 value 向量的集合，基于 query 向量对 value 向量进行加权平均，采用 q 和 v 计算注意力分数。
> - Transformer采用的是基于点积的注意力机制，给定 query 向量、key和value向量对的集合，采用q 和 k 的点积来计算注意力分数。

###### Scaled dot-product attention：引入放缩系数

随着key向量维度的增长，q 和 k 的点积得到的标量(值)的方差也会变大，如果直接作用到softmax函数，得到的概率分布会变得更加尖锐，大部分概率分布接近于0，导致梯度越来越小，不利于参数的更新。

所以在这个基础上，引入一个`scaled系数`，组成一个完整的Scaled dot-product attention模块，即在原有的基础上除以一个$\sqrt{d_k}$ ，即向量维度的开方。因此，最终的计算方式如下：
$$
\rm Attention(\it Q,K,V)=\rm softmax(\it
\frac {QK^T}{\sqrt {d_k}})V.
$$


###### Muti-Head Attention：多头注意力机制

<img src="https://raw.githubusercontent.com/baifeng66/photos/master/img/image-20241124095637283.png" alt="image-20241124095637283" style="zoom:50%;" />

Transformer采用了多个结构相同，但是参数不同的注意力模块(h个)组成多头注意力机制。每个注意力头的计算方式相同，但每个注意力头 i 对应权重矩阵的$W_i^Q,W_i^K,W_i^V$不同。

`即每个注意力头中 X 乘上的权重矩阵不同，每个注意力头进行不同的线性变换`。

<img src="https://raw.githubusercontent.com/baifeng66/photos/master/img/image-20241124095729284.png" alt="image-20241124095729284" style="zoom:50%;" />

- **Input**：如果是第一层Encoder，输入是embedding和位置编码的相加，非第一层的Encoder的输入是前一层的输出。
- **Scaled dot-product attention层**：计算注意力分数。
- **Concat层**：将注意力头得到的输出进行拼接，再通过线性层整合得到最终输出。
- **Output**：Muti-head attention模块的输出经过残差连接和正则化后，输入到后面的前馈神经网络。

###### 一个embedding计算例子

**Step1.** 计算单词Thinking、Machines的embedding，得到 ${x_1,x_2}$。

**Step2.** ${x_1,x_2}$ 与权重矩阵 $W^Q, W^K, W^V$相乘，得到 ${q_1,k_1,v_1}$。

**Step3.** 计算注意力分数，即${q_1,k_1}$的点积，这里假设${q_1 · k_1}=112,{q_2· k_2}=96$  （${q_1,k_1}$ 堆叠起来就是矩阵 Q,K ，即 ${QK}^T$ 计算的结果）。

**Step4.** 除以scaled系数dk，一般来说是向量维度的开方，这里假设dk=64。

**Step5.** 通过计算softmax得到注意力分数的概率分布：
$$
\rm softmax\it (x)=\frac{e^{x_i}}{\sum_i e^{x_i}},\rm softmax
(x_1)=\frac{e^{12}}{e^{14}+e^{12}}=0.88,\rm softmax
(x_2)=\frac{e^{14}}{e^{14}+e^{12}}=0.12.
$$
**Step6.** 将得到的概率分布矩阵与$v_1$ 相乘，得到最后的输出$z_1$ （矩阵V 是每个$v_1$ 堆叠起来的结果，相乘后的$z_1$ 堆叠起来的得到最后的输出矩阵 Z 。

<img src="https://raw.githubusercontent.com/baifeng66/photos/master/img/image-20241124100958766.png" alt="image-20241124100958766" style="zoom:50%;" />

##### Feed-Forward Network：前馈神经网络

这部分时两层的MLP层（全连接层），第一层的激活函数为 Relu，第二层不使用激活函数，对应的公式如下：
$$
max(0,XW_1+b_1)W_2+b_2.
$$

##### Add&Norm：残差连接与正则化

这一层包括`残差连接`（Residual connection）和`正则化`（Layer normalization），就是将每一小块的输入和这一小块的输出相加后再输入到下一块，即图中的红线部分。计算公式如下：

- 第一个Add&Norm层：$LayerNorm(X+MultiHeadAttention(X))$
- 第二个Add&Norm层：$LayerNorm(X+FeedForward(X))$

`残差连接`借鉴于CV领域中的ResNet，将输入输出直接相加，缓解模型过深导致的梯度消失问题。

`正则化`会将输入的向量变成一个均值为0，方差为1的分布，缓解梯度消失/爆炸问题。

更多细节可以参考[Transformer学习笔记](https://jiangcara.github.io/posts/bda47da5/)。

