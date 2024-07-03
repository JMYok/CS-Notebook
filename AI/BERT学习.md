# 什么是BERT
BERT（Bidirectional Encoder Representations from Transformers）是一种**基于Transformer架构**的自然语言处理（NLP）模型，由Google于2018年发布。它在多种NLP任务上表现出色，包括问答、情感分析和命名实体识别等。BERT的主要贡献在于其双向编码能力，使得它能够更好地理解上下文。
# BERT主要特点
1. 双向编码：传统的语言模型通常是单向的，即只从左到右（或从右到左）读取文本。BERT则使用了双向Transformer架构，可以同时考虑一个词的前后文信息，从而生成更丰富的词向量表示。
预训练和微调：
2. BERT首先在大规模文本语料库上进行预训练，之后可以在具体任务上进行微调。预训练阶段包括两个任务：掩码语言模型（Masked Language Model, MLM）和下一句预测（Next Sentence Prediction, NSP）。