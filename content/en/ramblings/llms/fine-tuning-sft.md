---
title: Fine-Tuning
linktitle: Supervised Fine-Tuning
toc: true
type: docs
date: "2019-05-05T00:00:00+01:00"
draft: false
menu:
  llms:
    parent: 5) Fine-Tuning
    weight: 14

# Prev/next pager order (if `docs_section_pager` enabled in `params.toml`)
weight: 14
---
In theory, the first paper to suggest **Supervised Fine-Tuning** was the [paper](https://arxiv.org/abs/2203.02155) introducing InstructGPT. However, their description of the fine-tuning procedure is definitely not clear to me. The paper introducing [**LoRA**](https://arxiv.org/abs/2106.09685) actually does a better job, in my opinion.

There are two key differences between **unsupervised pre-training** (UPT) and **supervised fine-tuning** (SFT):

1. *Training data*:
    - **SFT**: Each example in our dataset actually consists of a pair $(x, y)$ of text where $x$ is the *prompt* and $y$ is the *answer* that we wish our model to learn. For instance, $x$ could be a piece of text to summarise and $y$ could be its summary. As usual, these have been tokenized and are therefore *token indices*. 
    - **UPT**: Each example in our dataset is simply just one sequence of token indices. 
2. *Loss function*:
    - **SFT**: We use cross-entropy for next-token prediction, just like in UPT, except that we only do this for tokens in $y$ and neglect those in $x$. Below, I will write $\text{LLM}\_{\theta}(x_n, y\_{n, < t})\_{y\_{n, t}}$ for the logit obtained from the language model corresponding to the correct target token index $y\_{n, t}$ by feeding the concatenated token indices $[x_n, y\_{n, <t}]$, where we assume that $y\_{n, <1}$ is empty and adds nothing to $x_n$.
    $$
       \min\_{\theta} \left\\{ -\frac{1}{n\_{\text{batch}}}\sum\_{n=1}\^{n\_{\text{batch}}} \frac{1}{|y_n|} \sum\_{t=1}\^{|y_n|} \log\left(\text{softmax}(\text{LLM}\_\theta(x_n, y\_{n, <t})\_{y\_{n, t}})\right) \right\\}
    $$
    We are basically computing exactly the same loss, except that we don't compute it for the entire sequence but only for the "target/answer" part of the sequence.
    - **UPT**: Standard cross-entropy for next-token prediction, over the entire sequence. Here $\text{LLM}\_{\theta}\left(s_\{n, <t}\right)$ basically takes the place of 
      $$
          \min\_{\theta}\left\\{- \frac{1}{n\_{\text{batch}}\times T} \sum\_{n=1}\^{n\_{\text{batch}}}\sum\_{t=1}^T \log\left(\text{softmax}\left(\text{LLM}\_{\theta}\left(s_\{n, <t}\right)\_{s\_{n, t}}\right)\right)\right\\}
      $$

### Full Supervised Fine Tuning 
The automatic thing to do would be to then train all the parameters $\theta$ of the model using SFT. This is just as expensive (in terms of memory) as UPT, with the only difference that it will require far less iterations over the dataset (intuitively, because during UPT it has learned grammar, context, tonality etc, whereas for SFT we are just aiming to "nudge" the  model parameters in the right direction. 

This works well in terms of performance, but it can be prohibitive to do this for every downstream task. A better approach is **LoRa**.
