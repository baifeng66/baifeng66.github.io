+++
date = '2024-11-23T22:11:54+08:00'
draft = true
title = 'Transformers-Huggingface-实战'
categories = ["Python"]
tags = ["自然语言处理", "NLP"]
+++
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

