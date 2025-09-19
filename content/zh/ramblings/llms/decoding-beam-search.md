---
title: Decoding and Text Generation
linktitle: Beam Search
toc: true
type: docs
date: "2019-05-05T00:00:00+01:00"
draft: false
menu:
  llms:
    parent: 4) Decoding
    weight: 12

# Prev/next pager order (if `docs_section_pager` enabled in `params.toml`)
weight: 12
---
**Beam search** is a heuristic algorithm used to perform graph-search. **Stochastic Beam Search** was introduced as a heuristic to try and obtain a Maximum-A-Posteriori (MAP) solution to the problem of generating sequences from an LLM. The *key problem* with Beam Search is that it tends to produce outputs with **repetitions** and to get stuck in **loops**. Most importantly, there is a **lack of diversity** which can be attributed to the greedy optimization.
