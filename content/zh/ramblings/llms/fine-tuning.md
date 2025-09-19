---
title: Fine-Tuning
linktitle: Instruction Fine-Tuning
toc: true
type: docs
date: "2019-05-05T00:00:00+01:00"
draft: false
menu:
  llms:
    parent: 5) Fine-Tuning
    weight: 13

# Prev/next pager order (if `docs_section_pager` enabled in `params.toml`)
weight: 13
---
Paper: [Finetuned Language Models Are Zero-Shot Learners](https://arxiv.org/abs/2109.01652) </br>
Repo: [FLAN Instruction Fine-tuning Repo](https://github.com/google-research/flan)

LLMs are good at few-shot learning, but poor at zero-shot learning. The authors take a 137B pre-trained language model and perform **instruction fine-tuning** obtaining **FLAN** (Fine-tuned LAnguage Net). 

## 

## Instruction-FT vs Pretrain-FT vs Prompting
Given a pre-trained LLM the authors distinguish between:

1. *Pretrain Fine-Tuning* (e.g. BERT, T5): Fine-tune pre-trained LLM on a specific task A, and then test it by performing inference on that task. This model is specialized to task A and requires lots of task-specific examples.
2. *Prompting* (e.g. GPT-3): Use few-shot prompting or prompt engineering to improve model performance on task A.
3. *Instruction Fine-Tuning*: Instruction fine-tune on many tasks via natural language instructions, then perform inference on unseen task.

## Data preparation
Rather than creating datasets from scratch, they take publicly available datasets in `TensorFlow Datasets` and for each task type, they manually write $10$ unique instructions for that task. For diversity, for each task they include up to three instructions that are the "opposite" task. E.g. for Question-Answering, they could include one instruction that asks to generate a question from an answer.

## Evaluation
Datasets are clustered by task type and to evaluate the model on task A, say, the pre-trained LLM is instruction fine-tuned on all other clusters, and then tested on datasets in task A.

## Classification tasks
Most tasks requires to generate text, which is perfect because that is what the pre-trainde LM can do well. Some tasks require a "classification" output. In this case, they append a new token called `OPTIONS` at the end of the classification task, followed by a list of available options (e.g. "yes" and "no").

## Architecture
LaMDA-PT 137B with 2.49T BPE tokens, 32k vocabulary using `SentencePiece` library. Model pre-trained on web documents, dialog and Wikipedia, with 10\% non-English. FLAN is the instruction fine-tuned version of this model.

## Training
All datasets except those in the held-out cluster are mixed together and they randomly sample a batch from this mixed dataset during training. To balance the examples, they put a limit of $30k$ examples per dataset.
