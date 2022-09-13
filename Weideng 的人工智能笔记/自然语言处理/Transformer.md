## Transformer

Transformer 是经典的 SeqSeq 模型，输入一个 sequence ， 输出一个 sequence ，分为 encoder 和 decoder 两个部分。

## 结构总览

- encoder（图左）
- decoder（图右）

encoder 的 block 重复 $N$ 次，decoder 的 block 也要重复 $N$ 次。

![image-20220911142123567](images/Transformer/image-20220911142123567.png)

## Encoder

### Embedding

使用嵌入后的词向量。

### Positional Encoding

给输入的序列附加上**位置信息（Positional Information）**。

### Multi-Head Attention

多头注意力机制。

### Add

加法，线条连接表示**残差网络（Residual Network）**连接。

### Norm

标准化，不是 batch normalization ，而是 **layer normalization** ，对该层的经残差连接后的输出向量进行标准化。

## Decoder

以下以语音辨识（Speech Recognition）（输入一段声音，输出一段文字）为例。

文本字符集使用单位向量表示某一个字符。

### 输入输出方式

依据输入输出方式，Transformer 的 decoder 有两种：

- Autoregressive（AT）
- Non-autoregressive（NAT）

#### AT

![image-20220911161854034](images/Transformer/image-20220911161854034.png)

图中的 max 代表选取分数最高的单位向量，圆球代表 $1$ 。

- 初始输入
	- encoder 的输出。
	- 表示文本开始的文本字符集外的特殊符号（Begin of Sentence，BOS）（Special Token）
- 逐步输入
	- encoder 的输出。
	- 上一次 decoder 的输出。
- 逐步输出
	- 直到输出指定的结束符（END），也可使用 BOS 代替。

#### NAT

- AT 逐步输入，逐步输出
- NAT 一次输入一排 BOS，一次输出整个句子（词数与 BOS 数量相同）。

NAT 的输出长度有两种方式：

- 额外训练一个小模型用于预测长度。
- 设定一个较大的长度，然后忽略输出中 END 后的部分。

![image-20220912143124349](images/Transformer/image-20220912143124349.png)

NAT 的优势：

- 输出可并行计算。
- 输出长度更可控。

但 NAT 的效果一般不如 AT 。

### Masked Multi-Head Attention

masked attention 内部结构与一般 self-attention 一致，只是在每一次移动的当前位置，只输入该位置及之前的序列数据（前缀）。

即移动到第 $i$ 个位置时，使用第 $i$ 个 query ，只与前 $i$ 个 key 计算 score ，$i = 2$ 为例：

![image-20220912141141090](images/Transformer/image-20220912141141090.png)

一般的 self-attention 需要一次输入完整的序列数据，但 Transformer 的 encoder 逐步输出，decoder 逐步输入，在移动中只有已产生的数据，没有未产生的数据，故 decoder 的 attention 只能使用 masked attention 。

### Cross Attention

decoder 中承接 masked attention 的输出和 encoder 的输出的部分，称为 cross attention 。

![image-20220912145438837](images/Transformer/image-20220912145438837.png)

cross attention 部分对其输入，一起逐步做 self-attention 。

第一步：

![image-20220912145703932](images/Transformer/image-20220912145703932.png)

第二步：（后续同理）

![image-20220912145744825](images/Transformer/image-20220912145744825.png)

原始论文中 decoder 的每个 block 都输入最终 encoder 的输出，但可以有多种 cross attention 的方式：

![image-20220912150914225](images/Transformer/image-20220912150914225.png)

## 训练

### Transformer

每一次 decoder 产生的输出是一个表示词的向量，其各维的数值表示该维对应的单位向量的词的概率，整个向量表示一个概率分布。将这个向量与标准的单位向量做 cross entropy ，交叉熵越小，预测越准：（即是一次 Classification）

![image-20220912153748773](images/Transformer/image-20220912153748773.png)

需要对每次生成的词最小化 cross entropy ：

![image-20220912154331037](images/Transformer/image-20220912154331037.png)

### 对于训练 Seq2Seq 模型的 Tips

#### Copy Mechanism

例如在 chat-bot 中，出现一个特定的词组，机器不需要彻底理解它，只需要理解它的使用方式，在使用时从输入复制到输出即可：

![image-20220912155245900](images/Transformer/image-20220912155245900.png)

或是摘要任务（Summarization）中，需要识别特殊的关键词或语句。

实现可参见 Pointer Network 。

#### Guided Attention

在实际训练中，attention 可能导致 input 与 output 的对应不符合应当的方式，例如方向。

在语言识别（Speech Recognition），语音合成（Text to Speech，TTS）等任务中，input 和 output 应当是从左向右单调对齐的。

guided attention 可以为 attention 添加 input 和 output 对应方式的约束。

关键词：

- Monotonic Attention
- Location-aware Attention

#### Beam Search

假设字符集只有 A，B，输出一个句子的可能状态就是一颗树，当每一步都优先选分数高的路径时（贪心），最终的路径不一定是最好的。

同理，每次 minimize 当前的 cross entropy 不一定让整个句子的 cross entropy 最小。

![image-20220912214050163](images/Transformer/image-20220912214050163.png)

当字符集很大时，不可能穷举所有的状态，可以尝试 beam search，但 beam search 只更适用于有明确最优结果的任务，对于高创造性的任务需要添加随机性。

#### Sampling

对于 Sentence Completation 和 TTS 等高创造性任务，需要为模型添加随机性。

不只是 training 时添加 noise，在 test 时也要添加 noise 。

#### BLEU Score

- 双语替换评测（BiLingual Evaluation Understudy，BLEU）

Transformer 一般的损失函数为 cross entropy 。

可在 validation 和 test 时使用 BLEU Score ，即整个句子生成完后，再计算整个句子与 ground truth 的效果分数，效果比 cross entropy 好。

但 training 时仍需要使用 cross entropy ，因为 BLEU Score 不可微分，若一定要在 training 时使用 BLEU Score ，考虑强化学习方式。

#### Schedule Sampling

- Exposure Bias

	当在生成某个词时出错，如把“器”生成“氣”，则可能导致后续的词汇也出错，从而整个句子坏掉。

若机器只学习过完全正确的例子，则容易出现 exposure bias 这样泛化能力弱的情况。可给模型一些错误的样例，以提高模型泛化能力：

![image-20220913102807075](images/Transformer/image-20220913102807075.png)

- [Original Scheduled Sampling](https://arxiv.org/abs/1506.03099)

	原始版本。

- [Scheduled Sampling for Transformer](https://arxiv.org/abs/1906.07651)

	对 Transformer 特化的版本，会对并行能力有损害。

- [Parallel Scheduled Sampling](https://arxiv.org/abs/1906.04331)

	并行优化的版本。

