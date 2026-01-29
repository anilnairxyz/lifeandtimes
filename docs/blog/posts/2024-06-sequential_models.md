---
draft: false
date: 2024-06-05
slug: sequential_models
categories:
  - genai
tags:
  - ai
  - neural networks
  - transformers
  - rnn
---

# Sequential Generative Models

Autoregressive models inherently rely on sequential generation, where each new element is conditioned on the history of previously generated elements. While simple architectures like FVSBN and NADE demonstrate this principle, scaling to complex data like text and audio requires more sophisticated architectures capable of handling long-range dependencies and sequential data structures.

<!-- more -->

## Recurrent Neural Networks (RNNs)

Recurrent Neural Networks (RNNs) are designed specifically to handle **sequential data**. Unlike feedforward networks, RNNs maintain a **hidden state** that acts as a memory, capturing information about the sequence seen so far. This makes them naturally suited for time-series data, speech, and text.

For autoregressive applications, the RNN maintains a summary of the history in its hidden state and recursively updates it for each subsequent step in the sequence.

$$
h_t = f(h_{t-1}, x_t)
$$

However, standard RNNs suffer from significant limitations:

- **Bottleneck**: A single fixed-size hidden vector must store a summary of the entire history, which becomes difficult for long sequences.
- **Gradient Issues**: Training RNNs on long sequences often leads to **exploding or vanishing gradients**, making it hard to learn long-range dependencies.

## Attention-Based Models

To overcome the specific limitations of RNNs, **attention mechanisms** were introduced. Instead of relying on a single compressed hidden state to capture the entire history, attention allows the model to dynamically look back at parts of the input sequence that are relevant to the current prediction.

The core idea is to:

1.  Compare the current state to all previous hidden states.
2.  Compute an **attention distribution** (weights) indicating which previous states are most relevant.
3.  Construct a context vector as a weighted sum of these previous states.
4.  Use this context vector to predict the next token.

This mechanism "frees" the model from the bottleneck of a single hidden state, allowing it to access information from any point in the past directly.

## Generative Transformers

The **Transformer** architecture revolutionised sequential modeling by relying entirely on self-attention mechanisms, discarding recurrence altogether.

- **Self-Attention**: Allows each position in the sequence to attend to all positions, capturing complex relationships regardless of distance.
- **Masked Self-Attention**: Crucial for generative tasks, masking ensures that the model cannot "see into the future". When predicting position $t$, it can only attend to positions $< t$, preserving the autoregressive property.

Transformers have become the backbone of modern Large Language Models (LLMs) like GPT, demonstrating unprecedented capabilities in text generation.

## Convolutional Architectures

While RNNs and Transformers process sequences, **Convolutional Neural Networks (CNNs)**—typically used for images—can also be adapted for sequence generation to overcome the sequential slowness of RNNs.

- **Masked Convolutions**: Standard convolutions use information from surrounding pixels (both past and future in a sequence context). To be autoregressive, convolutions must be masked so that the receptive field at time $t$ only covers inputs from time $< t$.
- **Parallelism**: Unlike RNNs, convolutional computations can often be parallelised more effectively during training, though generation remains sequential.
- **Ordering Issues**: For 2D data like images, defining a strict "order" (e.g., raster scan) is somewhat arbitrary, which remains an open challenge for autoregressive image models.

Sequential generation remains a fundamental constraint of autoregressive models—whether RNN, Transformer, or CNN-based—requiring $O(N)$ steps to generate a sequence of length $N$.
