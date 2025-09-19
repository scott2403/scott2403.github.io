---
title: Landmark Papers
linktitle: GPT papers
toc: true
type: docs
date: "2019-05-05T00:00:00+01:00"
draft: false
menu:
  llms:
    parent: 7) Landmark Papers
    weight: 19

# Prev/next pager order (if `docs_section_pager` enabled in `params.toml`)
weight: 19
---

- [Improving Language Understanding by Generative Pre-Training](https://s3-us-west-2.amazonaws.com/openai-assets/research-covers/language-unsupervised/language_understanding_paper.pdf) - *Radford 2018, OpenAI* - **GPT**, context $512$. This is a **decoder-only** architecture. Training constists of two stages:
    1. *unsupervised pre-training*: Uses the language modeling objective on unlabeled data, to provide a good initialization for the parameters. They maximise the likelihood
        $$
            \sum_{i} \log P(u_i \mid u_{i-k}, \ldots, u_{i-1}; \Theta), \qquad k \text{ context window}
        $$
    2. *supervised fine-tuning*: Use the supervised objective with a manually annotated datasets, depending on the target task. Each sequence $x^1, \ldots, x^m$ in the supervised dataset has an associated label $y$. The sequence is fed through the Transformer to obtain the final activation $h_l^m$. This is then fed through another, **task-specific**, linear layer with matrix $W_y$, and then through a soft-max. For some tasks, which are very structured, they require some modifications at the end of the network. We then maximise a mix between supervised and unsupervised
        $$
            \left[\sum_{(x,y)} \log \text{softmax}(h_l^m W_y)\right] + \lambda \sum_{i} \log P(u_i \mid u_{i-k}, \ldots, u_{i-1}; \Theta)
        $$
- [Language Models are Unsupervised Multitask Learners](https://d4mucfpksywv.cloudfront.net/better-language-models/language_models_are_unsupervised_multitask_learners.pdf) - *Radford 2019, OpenAI* - **GPT-2**, 1.5B parameters, pre-LN, context $1024$, batchsize $512$. Scale allows GPT-2 to achieves SOTA on language modelling datasets that it was not trained on, thus it is a **zero-shot** learner. 
    - *Data*: To improve CommonCrawl, they only include documents that have been curated/filtered by humans: they scraped all outbound links from Reddit which received at least 3 karma, up to December 2017, and removed all Wikipedia links to avoid training on data it will be evaluated on. The resulting dataset is called **WebText** and includes 8 million documents, with a total of 40GB.
    - *Encoding*: They use a modification of Byte-Pair Encoding (BPE)
    - *Residual Scaling*: In deep networks residual connections accumulate, thus they get initialized at $1/\sqrt{N}$ where $N$ is the number of residual layers.
- [Language Models are Few Shot Learners](https://arxiv.org/pdf/2005.14165): 


