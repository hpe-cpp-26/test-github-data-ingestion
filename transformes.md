# Understanding the Transformer Architecture
The Transformer is a deep learning architecture introduced in the 2017 seminal paper "Attention Is All You Need" by Vaswani et al. Unlike its predecessors (RNNs, GRUs, and LSTMs) which process data sequentially, the Transformer relies entirely on Self-Attention mechanisms to process sequence data in parallel, drastically reducing training times and improving long-range dependency modeling.

## 1. High-Level Architecture
The original Transformer uses an Encoder-Decoder structure:

Encoder: Processes the input sequence and maps it into a continuous representation that holds the contextual information of the inputs.

Decoder: Uses the encoder's representation along with previous outputs to generate an output sequence, one token at a time.

2. Core ComponentsA. Embedding and Positional EncodingSince Transformers process all tokens simultaneously, they have no inherent sense of word order or position. To fix this, two steps are required:Input Embedding: Converts tokens (words/sub-words) into continuous vectors of a fixed size ($d_{model}$).Positional Encoding: Adds a unique position-dependent vector to each token embedding. These vectors are generated using sine and cosine functions of different frequencies:$$PE_{(pos, 2i)}
3. = \sin\left(\frac{pos}{10000^{\frac{2i}{d_{model}}}}\right)$$$$PE_{(pos, 2i+1)} = \cos\left(\frac{pos}{10000^{\frac{2i}{d_{model}}}}\right)$$B.
4. Self-Attention MechanismThe core engine of the Transformer is the Scaled Dot-Product Attention. It allows the model to look at other words in the
5.  input sequence to get a better encoding for the current word.For every input token vector, three new vectors are created:Query ($Q$):
6.  What the token is looking for.Key ($K$): What the token contains/offers.Value ($V$): The actual content or meaning of the token.
7.  The attention weights are calculated as:$$\text{Attention}(Q, K, V) = \text{softmax}\left(\frac{QK^T}{\sqrt{d_k}}\right)V$$Where $d_k$ is the
8.  dimensionality of the key vectors. Dividing by $\sqrt{d_k}$ scales down the dot products to prevent the gradients from vanishing during softmax.C.
9.   Multi-Head AttentionInstead of performing a single attention function, Multi-Head Attention projects the queries, keys, and values $h$ times with different, learned linear projections.
10.    This allows the model to jointly attend to information from different representation subspaces at different positions.$$\text{MultiHead}(Q, K, V) = \text{Concat}(\text{head}_1, \dots, \text{head}_h)W^O$$$$\text{where } \text{head}_i = \text{Attention}(QW_i^Q, KW_i^K, VW_i^V)$$D. Feed-Forward Networks (FFN)Each layer in both the encoder and decoder contains a fully connected feed-forward network.
11. This consists of two linear transformations with a ReLU (or GeLU in modern variants) activation in between:$$\text{FFN}(x) = \max(0, xW_1 + b_1)W_2 + b_2$$3. Detailed Step-by-Step ExecutionThe Encoder BlockEach encoder layer has two main sub-layers:Multi-Head Self-AttentionPosition-wise Feed-Forward NetworkAround each sub-layer, a residual connection is utilized, followed by Layer Normalization:$$\text{Output} = \text{LayerNorm}(x + \text{SubLayer}(x))$$The Decoder BlockIn addition to the two sub-layers found in the encoder, the decoder inserts a third sub-layer:Masked Multi-Head Self-Attention: Prevents positions from attending to subsequent positions. This ensures that the predictions for position $i$ can depend only on the known outputs at positions less than $i$.Encoder-Decoder Attention: Performs multi-head attention over the output of the encoder stack using queries from the decoder and keys/values from the encoder.Position-wise Feed-Forward Network.
