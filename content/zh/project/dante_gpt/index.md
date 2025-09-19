---
title: Dante GPT
summary: LLM trained on Dante Alighieri's Divina Commedia using Pytorch.
tags:
  - Large Language Models
  - GPT
  - Attention
  - Transformers
  - NLP
date: '2024-06-02T00:00:00Z'

# Optional external URL for project (replaces project detail page).
external_link: ''

image:
  caption: A GPT trained on Divina Commedia
  focal_point: Smart
  width: 100
  height: 150

links:
  - icon: github
    icon_pack: fab
    name: Project Code
    url: https://github.com/MauroCE/DanteGPT
  - icon: twitter
    icon_pack: fab
    name: Follow
    url: https://twitter.com/MauroCamaraE
url_code: ''
url_pdf: ''
url_slides: ''
url_video: ''

# Slides (optional).
#   Associate this project with Markdown slides.
#   Simply enter your slide deck's filename without extension.
#   E.g. `slides = "example-slides"` references `content/slides/example-slides.md`.
#   Otherwise, set `slides = ""`.
slides: example
---
The aim of this project is to write from scratch and test various model design choices for LLMs. My application will be [DanteGPT](https://github.com/MauroCE/DanteGPT), i.e. creating a chatbot that generates text similar to Dante Alighieri's "Divina Commedia". I want to test both training speed, validation/test loss and ease of use/tuning. I am running all examples on a MacBook Pro M3 with 11 cores.


# Model 1
My first model is very similar to Andrej Karapathy's NanoGPT, which is itself very much inspired by the architecture of GPT2, with some refinements. The full model is described in the GitHub repo, but here is a diagram explain the main features.

![DanteGPT](model1.png)


# Going Forward
There are several design choices that would be more modern, and I plan to explore these in the next few weeks. 

