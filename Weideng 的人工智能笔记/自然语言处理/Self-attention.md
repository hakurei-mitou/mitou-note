# Self-attention

- 自注意力（Self-attention）

以下以 Sequence Labeling 为例。

## 语义关联

以词性标注（Part-of-Speech Tagging，POS Tagging）为例。

相同单词在一个句子中可能呈现不同的词性，甚至需要联系整个 Sequence 判断，不能逐个输入单个向量进行标注。

若考虑单词的前后邻居，可能不够准确；若考虑设定一个窗口值，每次输入的 Sequence 可能不同，若将窗口设置得很大，会导致模型参数过多。

可以使用 Self-attention 机制。

## 向量转化

将整个 Sequence 输入 Self-attention 层，Self-attention 将每个向量转化为新的考虑了整个 Sequence 的向量，然后再逐个向量输入全连接网络（FC）：

![image-20220725220105961](images/Self-attention/image-20220725220105961.png)

Self-attention 层任意安排次数和位置：

![image-20220725220405175](images/Self-attention/image-20220725220405175.png)
