> 词频 词序 上下文

马尔可夫假设（Markov Assumption）：下一个词的出现只与前面 n-1 个词有关。满足这个假设的模型称为 N元文法（N-gram）模型。 $P（w_i|w_1w_2...w_{i-1} \approx P（w_i|w_{i-n+1}...w_{i-1}）$

> uni-gram、bi-gram、tri-gram、4-gram、5-gram

bi-gram 模型成为一阶马尔可夫链，tri-gram 模型成为二阶马尔可夫链……

为了防止出现0概率，会应用平滑（Smoothing）技术，如拉普拉斯平滑。

语言模型性能评价分为 外部评价 和 内部评价。外部评价是通过应用于具体任务来评价模型性能，代价较高；常用基于困惑度（Perplexity，PP）的内部评价。困惑度就是计算测试集每个词的几何平均的倒数（乘积开方取倒数）。


---

自然语言处理一般首先都需要将输入的每个语言符号（token），映射为一个索引值。所以通常可以在代码中看到一个 Vocabulary 类，它统计输入文本的所有词，然后给每个词一个索引值。称为词表映射。

但是词的表示不可能就用一个索引值，所以需要将词映射为一个向量，把这个映射过程也放入网络中训练，这就是词向量层（Embedding Layer），所有通常看到代码中有 `Embedding(vocab_size, embedding_dim)` 这样的层。

数据处理中一般会定义一个数据加载器，要求输入一个Dataset类、批次和collate函数。Dataset类用于加载数据，collate函数用于将一个批次的数据进行整理，指明一个批次的数据应该是什么样的，比如会返回 `inputs, targets` 等，序列的对齐、填充等操作也可以在这里进行。

---

分布式词向量表示方法 静态词向量预训练模型

Word2Vec（Word to Vector）/ Doc2Vec（Document to Vector）

Word2Vec是从大量文本语料中以无监督的方式 **学习语义知识** 的一种模型。

先把各个词利用one-hot编码表示，在大语料库上进行神经网络训练，最后得到的权重矩阵就是我们想要的东西。

目标：将 $V$ 维的词向量，映射到一个 $N$ 维的空间，且要使得词向量之间的距离能够反映词之间的语义关系。

> one-hot编码中其为词汇表大小，所以这里 $V$ 为词汇表大小

具体方法有两个，CBOW（Continuous Bag of Words）和Skip-gram。CBOW的网络是输入词的上下文，输出是目标词；Skip-gram的网络是输入目标词，输出是上下文。

CBOW的网络结构如下：

输入 $X$ ，经过权重矩阵 $W_1$ 和求均值为 $H$ ，再经过权重矩阵 $W_2$ 和激活函数变为 $Y$。即 $Y = HW_2$ ， $H = MEAN(XW_1)$ ，

其中 $X$ 是一个 $C\times V$ 的矩阵，$C$ 为上下文词数量；$W_1$ 是一个 $V\times N$ 的矩阵；$H$ 是一个 $1\times N$ 的向量；$W_2$ 是一个 $N\times V$ 的矩阵；$Y$ 是一个 $1\times V$ 的向量。

训练结束后，我们得到了一个这样的模型：输入某个词的上下文（一堆one-hot编码的词），预测出这个词。说明这个模型的权重包含了某个词与其上下文之间的关系，而恰好模型里面的权重矩阵 $W_1$ ，它当中的每一行无论是在训练还是推理都只会与一个词有关，所以 $W_1$ 的行向量与词向量之间有一个一一对应的关系。故我们可以用 $W_1$ 的行向量作为词向量，而这个行向量就是 $N$ 维的。

Skip-gram的网络类似，不够 $X$ 是一个 $1\times V$ 的向量，$W_1$ 和 $W_2$ 的维度不变，$Y$ 是一个 $1\times V$ 的矩阵。即 $Y = XW_1W_2$ 。（这里输出的 $Y$ 的每个值代表作为输入词上下文的 对应词的 概率）

同理，我们可以用 $W_1$ 的行向量作为词向量。

> 可以想想 $W_2$ 的含义

缺点：
1. 词向量唯一，无法区分多义词
2. 无法处理未知词
3. 忽略了词序
4. 需要大量数据
5. 词向量本身难以解释

*TODO：有空补图片*

---

GloVe（Global Vectors for Word Representation）




---

先说一下LSTM（Long Short-Term Memory），LSTM就是在循环神经网络（RNN）的基础上加入了门控机制，解决了RNN的长期依赖和梯度消失问题。LSTM加入了三个门：输入门、遗忘门、输出门。

传递信息的更新为 上次信息 * 遗忘门 + 本次信息 * 输入门。激活后再乘以输出门作为当前阶段的输出信息。（传递信息和输出信息不一样，当然也看任务）

其中各种什么信息、门之类的都是 以当前词向量输入及上次阶段的输出信息做输入的神经网络计算得到的。

定义各权重矩阵：

$W^{xh}$ 是输入词向量到隐藏层的，$W^{hh}$ 是隐藏层到隐藏层的，$W^{hy}$ 是隐藏层到输出层的； $W^{fx}$ 是遗忘门对输入词向量的，$W^{fh}$ 是遗忘门对上次输出信息的； $W^{ix}$ 是输入门对输入词向量的，$W^{ih}$ 是输入门对上次输出信息的； $W^{ox}$ 是输出门对输入词向量的，$W^{oh}$ 是输出门对上次输出信息的。

更新公式：

$$
\begin{cases}
    f_t = \sigma(W^{fx}x_t + W^{fh}h_{t-1}), & \text{遗忘门} \\
    i_t = \sigma(W^{ix}x_t + W^{ih}h_{t-1}), & \text{输入门} \\
    \tilde{C}_t = \tanh(W^{xh}x_t + W^{hh}h_{t-1}), & \text{新信息} \\
    C_t = f_t \odot C_{t-1} + i_t \odot \tilde{C}_t, & \text{传递信息} \\
    \\
    o_t = \sigma(W^{ox}x_t + W^{oh}h_{t-1}), & \text{输出门} \\
    h_t = o_t \odot \tanh(C_t), & \text{输出信息}

\end{cases}
$$

LSTM只能在一定程度上缓解RNN中的长距离依赖问题。

基于循环神经网络的序列到序列模型，输入的序列经过一个循环神经网络（编码器）得到最终的输出信息 $y$ ；认为这个 $y$ 包含了输入序列的所有信息；此后，将其作为另一个循环神经网络（解码器）的传递信息输入，定义第一个输入信息，每次输出一个信息，并把这个信息作为下一个输入信息，直到输出序列结束。

*TODO：有空补图片*


注意力机制（Attention Mechanism），让模型对以前的信息有偏重的关注。前面基于RNN的序列到序列模型，信息的生成仅基于前一个状态即其输出信息，而注意力机制还会考虑上源输入序列的所有信息。

引入了注意力机制的RNN：
****
*TODO*

自注意力机制（Self-Attention Mechanism）

抛弃了RNN的顺序性，直接对输入序列进行处理，每个词都可以与其他词进行交互。

$$
\bm{y_i} = \sum_{j=1}^{n} \alpha_{ij}\bm{x_j}
$$

其中 $\bm{x_j}$ 为第 $j$ 个输入向量；$\alpha_{ij}$ 为第 $i$ 个输入向量与第 $j$ 个输入向量之间的注意力权重，通过注意力计算公式和softmax函数得到。列归一。

计算 $\alpha$ 需要两个向量，一般称作 $Q$ 和 $K$ ，由输入向量 $\bm{x}$ 通过映射矩阵得到。输入向量也可以经由一个映射矩阵后再进行输入。

多头自注意力机制（Multi-Head Self-Attention Mechanism）就是设置多组映射矩阵，产生多个注意力再映射回相应的维度。

一个Transformer块就是多个自注意力层和前馈神经网络层的堆叠。输入还另有一个位置编码。

Transformer模型分成编码器和解码器两部分，编码器由多个Transformer块组成，解码器也是。解码器还有一个额外的注意力层，用来关注编码器的输出。

只有编码器是并行计算，解码器是串行计算。

以中英机器翻译举例，输入中文句子，输出英文句子。编码器对整个中文句子进行编码，解码器先接受一个特殊的开始符号，然后根据编码器的输出和自身的输出逐个地生成英文句子的词。

---

BERT(Bidirectional Encoder Representations from Transformers) 是一个双向的 Transformer 模型，通过 Masked Language Model 和 Next Sentence Prediction 两个任务进行预训练。

---

生成模型

# Reference
