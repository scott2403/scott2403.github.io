---
title: High-Level Overview
linktitle: LLMs
toc: true
type: docs
date: "2019-05-05T00:00:00+01:00"
draft: false
menu:
  llms:
    parent: 1) High-Level Overview
    weight: 1

# Prev/next pager order (if `docs_section_pager` enabled in `params.toml`)
weight: 1
---
### <span style="color:#2AB7CA">Vocabulary</span>
I will write $\mathcal{V}$ to denote a **vocabulary**: a set of objects, known as **tokens**. For instance:
 1. $\mathcal{V}$ could contain the alphabet, digits $0$-$9$, and the remaining symbols on your keyboard. 
 2. $\mathcal{V}$ could additionally contain all the words in the Oxford dictionary.
 3. $\mathcal{V}$ could additionally contain all the suffixes and prefixes such as `"ing"` and `"un"`.
 
In practice, the number of tokens in the dictionary is around $50k$, although it varies a lot based on implementation. In code I will write `n_vocab` for the size of the vocabulary, in math formulas $n_{\text{vocab}}$, you can find a list of notation later in this section of the course.

I will use the symbol $\mathbf{t}$ to denote a token in the vocabulary, $\mathbf{t}\in\mathcal{V}$.

### <span style="color:#2AB7CA">Token Indices</span>
In reality the LLM does not work directly with the tokens, since these are typically *strings* and we want to work with **numbers**. Instead, we **sort the vocabulary** into a long sequence of tokens (the order is not important)
$$
\mathcal{V}\^{\text{sorted}} := (\mathbf{t}_1, \ldots, \mathbf{t}\_{n\_{\text{vocab}}})
$$
and then we associate each token with its corresponding index, so that $10$ identifies $\mathbf{t}\_{10}$, the $10\^{\text{th}}$ token in the sorted vocabulary $\mathcal{V}\^{\text{sorted}}$. I will call these indices simply **token indices**. The *set* of token indices is denoted by
$$
\mathcal{S} := \left\\{1, 2, 3, \ldots, n\_{\text{vocab}}\right\\}.
$$
and I will write $s\in\mathcal{S}$ for token indices.

> The input to the LLM are token indices.

### <span style="color:#2AB7CA">Embeddings</span>
I hope you agree that it is a bit awkward to work with **integers**. Internally, the LLM will associate to each of these indices a vector. This vectors will be known as **embeddings** of the tokens and they will have length $n_{\text{emb}}$, which will be chosen by the researcher. Therefore each token $\mathbf{t}_s$ has an associated embedding vector $\mathbf{e}_s$. 

This happens *internally* the LLM, remember, the input to the LLM are the token indices.

### <span style="color: #FE4A49">Large Language Model</span>
> A Large Language Model (LLM) is a function that maps a sequence of tokens indices to an unnormalized log-probability vector over the set of token indices, representing the un-normalized log-probability of the next token index in the sequence.

The LLM accepts sequences of token indices of length up to $T$, where $T$ is known as the **context size**, which is chosen by the researcher based on performance and hardware considerations. I will write
$$
\mathcal{S}\^{\leq T} := \left\\{(s_1, \ldots, s_t)\\,:\\, s_i\in\mathcal{S}\\, \\,\\, \forall i=1, \ldots, t, \text{ and } \\, 1\leq t \leq T, \\, t\in \mathbb{Z}_+\right\\}
$$
for the domain of the Large Language Model.

> A LLM is a parametrized function $\text{LLM}\_\theta:\mathcal{S}\^{\leq T}\to \mathbb{R}^{n\_{\text{vocab}}}$.

Notice that we typically call the output of the LLM **logits** and it is very easy to recover probabilities from them, one just needs to feed them through the **softmax** function
$$
\text{softmax}(\ell\_1, \ldots, \ell\_{n\_{\text{vocab}}}) = \left(\frac{\exp(\ell_1)}{\displaystyle \sum\_{j=1}\^{n\_{\text{vocab}}} \exp(\ell_j)}, \ldots, \frac{\exp(\ell_{n\_{\text{vocab}}})}{\displaystyle \sum\_{j=1}\^{n\_{\text{vocab}}} \exp(\ell_j)}\right)
$$

### <span style="color: #FE4A49">Training Data</span>
The training data for a LLM is typically loads of text scraped from the internet, for simplicity imagine that you have a very large `.txt` file, which you read in memory as a very large string. Before performing any training, you need to transform this large *string* into *numbers*. The string is then **tokenized**: it is converted in a sequence of token indices
$$
\texttt{training_data.txt} \longrightarrow \text{"Hello..."} \longrightarrow (7, 4, 11, 11, 14, \ldots).
$$
The length of this sequence will be the number of tokens needed to encode the training data, which we assume to be $n\_{\text{train}}$. Therefore our training data is the following sequence of token indices
$$
\mathcal{D}\_{\text{train}} = (s\_1, \ldots, s\_{n\_{\text{train}}}).
$$

### <span style="color: #FE4A49">Training Dynamics</span>
The parameters $\theta$ are initialized randomly using various heuristics and then they are learned in an unsupervised (or rather, self-supervised) fashion using back-propagation on a loss function, typically the **cross-entropy** loss. 

Training happens in **batches**. The researcher will choose a **batch size**, $n\_{\text{batch}}$, again typically based on hardware considerations. The idea behind batching is to simply process $n\_{\text{batch}}$ independent training examples simultaneously, *in batch*. I will describe **what happens for a single training example** now, but then will discuss how to generalize this for batching. At the very beginning of each training iteration, we do as follows:
1. Sample a token index uniformly from the tokenized training set
$$
\begin{align}
    s_i \in \mathcal{D}\_{\text{train}} \qquad\text{ where } \qquad i &\sim \text{Uniform}\left(\\{1, 2, 3,\ldots, n\_{\text{train}}\\}\right)
\end{align}
$$
2. Construct two sequences of length $T$
$$
\begin{align}
    \mathbf{s}_i = (s\_{i}, \ldots, s\_{i+T}) \qquad \text{ and } \qquad \mathbf{y}_i = (s\_{i+1}, \ldots, s\_{i+T+1})
\end{align}
$$
The second sequence is shifted by one with respect to the first sequence. This is because we train the LLM using self-supervised learning which in this case means that we want the LLM to learn to predict the next token index given a previous sequence of token indices of length at most $T$. These two sequences together contain $T$ training examples: $y\_{i, t}$ for $t = i+1, \ldots, t+T+1$ corresponds to the next token index that appears after $\mathbf{s}\_{i, 1}, \ldots, \mathbf{s}\_{i, t}$ in the training data. I will generally call $\mathsf{s}_i$ the **input** and $\mathsf{y}_i$ the **target**.

When using batching, we repeat this process $n\_{\text{batch}}$ times independently. Typically we stack the vectors so that the training input and training target of the LLM at each iteration both have shape $(n\_{\text{batch}}, T)$.

> In practice, the input and target of the LLM is a matrix of shape $(n\_{\text{batch}}, T)$. 

### <span style="color: #FE4A49">Loss Function</span>
How do we learn $\theta$? Take a batch $\mathbf{s}$ of token indices and target indices $\mathbf{y}$ from the training data, both have shape $(n\_{\text{batch}}, T)$. Then:

1. Feed $\mathbf{s}$ through $\text{LLM}\_\theta$ and obtain a vector of **logits** of size $n\_{\text{vocab}}$ for each training example in the batch and for each time-step from $1$ to $T$. That is, we have $(n\_{\text{batch}}, T)$ log-probability vectors, which we can stack into a single tensor $\boldsymbol{\ell}$
$$
\boldsymbol{\ell} = \begin{pmatrix}
    \boldsymbol{\ell}\_{1, 1} &\cdots  & \boldsymbol{\ell}\_{1, T} \\\\
    \vdots & \ddots & \vdots \\\\
    \boldsymbol{\ell}\_{n\_{\text{batch}}, 1} &\cdots  & \boldsymbol{\ell}\_{n\_{\text{batch}}, T}
\end{pmatrix} \in\mathbb{R}^{(n\_{\text{batch}}, T, n\_{\text{vocab}})}
$$
2. Compute the cross-entropy loss (`F.cross_entropy`).
$$
\mathcal{L} = -\frac{1}{n\_{\text{batch}} \times T}\sum\_{n=1}\^{n\_{\text{batch}}} \sum\_{t=1}^T \log\left(\text{softmax}\left(\boldsymbol{\ell}\_{n, t, \mathbf{y}\_{n, t}}\right)\right)
$$
where $\boldsymbol{\ell}\_{n, t, \mathbf{y}\_{n, t}}$ is the $\mathbf{y}\_{n, t}$-entry of the vector $\boldsymbol{\ell}\_{n, t}$. That is, we compute the average negative log-probability of the correct token index ($\mathbf{y}\_{n, t}$) under the model.

We then back-propagate the gradient of this loss to update $\theta$ using e.g. **Adam**.

### <span style="color: #3F6634">Text Generation</span>
Once the model is trained, generation is straightforward once the user will provide a prompt of length $n\_{\text{prompt}}$. 


1. If $n\_{\text{prompt}} > T$ then we simply crop the string and grab the final $T$ characters of that string, otherwise we leave it unchanged.
2. This (potentially truncated) string will be tokenized, reshaped to have shape 
$$
(1, \text{min}(n\_{\text{prompt}}, T), )
$$
3. This will be fed through the network to obtain logit vectors $\boldsymbol{\ell}_1, \ldots, \boldsymbol{\ell}\_{n\_{\text{prompt}}}$. We are only interested in generating text that **follows** the user-provided prompt, so really we are only interested in $\boldsymbol{\ell}\_{n\_{\text{prompt}}}$ as this contains the un-normalised log-probabilities for the index of the next token.
4. We now simply compute the normalized probabilities over the token indices and sample a new token index
$$
s_{n\_{\text{prompt}}+1} \sim \text{Categorical}(\text{softmax}(\boldsymbol{\ell}\_{n\_{\text{prompt}}})).
$$
We then use this token index to grab the corresponding token $\mathbf{t}\_{s_{n\_{\text{prompt}}+1}}$ which we append to the (potentially cropped) user prompt and repeat from step 1.


### Content of the course
In the rest of the course, I aim to cover:

1. *Tokenizers*: How to map text to indices? 
2. *Embeddings*: How do we map token indices $s_t$ to their embedding $\mathbf{e}_s$?
3. *Positional Encoding*: How do we encode positional information into the embeddings?
4. *LLM structure*: What's the inner structure of an LLM and what design options do we have?
5. *Unsupervised Pre-Training*: How do we teach the model to sample sensible token indices?
6. *Supervised Fine-Tuning*: After pre-training how do we specialize our LLM to our task?
7. *Alignment*: After fine-tuning, how do we make sure that the LLM's output is not harmful?


