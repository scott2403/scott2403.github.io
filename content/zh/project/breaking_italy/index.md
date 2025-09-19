---
title: Exploring News Through Data
summary: LDA & Sentiment Analysis on YouTube comments.
tags:
  - NLP
  - Data Journalism
  - Breaking Italy
  - YouTube Data API
  - Sentiment Analysis
  - Latent Dirichlet Allocation
  - API
date: '2023-08-15T00:00:00Z'

# Optional external URL for project (replaces project detail page).
external_link: ''

image:
  caption: Breaking Italy keyword cloud
  focal_point: Smart
  width: 100
  height: 150

links:
#  - icon: github
#    icon_pack: fab
#    name: Project Code
#    url: https://github.com/MauroCE
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

Breaking Italy has been my favorite infotainment YouTube channel since I was in high school. It's one of the largest YouTube communities in the Italian scene, and it features a mix of news and opinions. What I love the most about the channel is that Alessandro Masala, the creator and host of the show, always makes an effort to provide the backstory for the news, and this allows you to have an eagle-eye on what is happening in the world. Alessandro also provides a wealth of references that any user can use to read more about each topic discussed in the video.

I have often found myself **looking for a reference**, be it a newspaper article or a report, that I wanted to read again but I was unable to because I had no way to quickly search through the thousands of videos Breaking Italy has posted over the years. This presented me with a great opportunity to try the YouTube Data API and use my coding skills. At the time, I was mostly interested in finding a Gender Gap report that Alessandro talked about in one of his videos.


### What's in the Mix
I rolled up my sleeves and jumped into the data behind the scenes. With 1068 videos in the mix, I took the YouTube Data API for a spin, snagging titles, keywords, and descriptions. Here's three tasks I worked on:

1. **WORD CLOUD SYNTHESIS**: For each of the 1068 videos, I have extracted keywords from the description, the title and the YouTube keywords. I have used this information to craft a word cloud that captures the essence of those videos in a single visual. [Give it a go](https://colab.research.google.com/drive/16yfS7oRSjJbhcZGoKDyWco1rn2pBEiXu?usp=sharing).

2. **REFERENCES SEARCH ENGINE**: Each video provides a wealth of references used to inform the journalist. These are often gathered by topic and may or may not have a description. The way these references have been provided has changed multiple times throughout the years, in other words this required messy RegEx and text processing. [Try the code](https://colab.research.google.com/drive/1ldJPBgfyRRGr2QYfp8zg6Ar0PPefn3vr?usp=sharing).
    1. Extracted URLs from video descriptions.
    2. Inferred their content based on description text, video title and keywords. 
    3. Generated a dictionary with keywords as keys and lists of URLs matching those keywords as values.
    4. Wrote Javascript software for a [Search Bar](https://maurocamaraescudero.netlify.app/breaking_italy.html) that finds all the URLs matching the intersection of the keywords provided by the user.

3. **SENTIMENT ANALYSIS AND LDA**: Breaking Italy's videos usually feature 3 major news and some smaller ones. I decided to start with a monothematic episode "What happened in Russia with the Wagner company?". 
    1. Processed comments' emojis using the [emojy](https://pypi.org/project/emoji/) package.
    2. Removed Italian stopwords using the [NLTK](https://www.nltk.org/api/nltk.html).
    3. Latent Dirichlet Allocation with [GenSim](https://pypi.org/project/gensim/).
    4. For each topic, run Sentiment Analysis in Italian with [Polyglot](https://polyglot.readthedocs.io/en/latest/Sentiment.html).
    5. Visualization with [pyLDAvis](https://pypi.org/project/pyLDAvis/#:~:text=pyLDAvis%20is%20designed%20to%20help,an%20interactive%20web%2Dbased%20visualization).
    
    Of course, there are many limitations to this. First and foremost, working with social media data, such as comments, can be messy due to high levels of sarcasm, inner-jokes, and general internet references (memes, emojis, set phrases). Another limitation is that these videos don't have very large numbers of comments, limiting the generalization of the results. Nonetheless, I found the results very interesting. It is generally quite difficult to give a rule-of-thumb for the number of topics for LDA, but upon reading the comments myself, I thought there would be less than $5$ major topics. Using $n=3$ we obtain the following output (translated by me):
  ```
  LDA Topics:
  (0, '0.019*"joy" + 0.019*"tears" + 0.007*"video" + 0.006*"state" + 0.005*"before" + 0.005*"mercenaries" + 0.005*"never" + 0.004*"do" + 0.004*"toilet" + 0.004*"Moscow"')
  (1, '0.007*"history" + 0.006*"only" + 0.005*"Russia" + 0.005*"war" + 0.005*"state" + 0.005*"mercenaries" + 0.004*"me" + 0.003*"Moscow" + 0.003*"part" + 0.003*"video"')
  (2, '0.006*"thanks" + 0.006*"work" + 0.005*"always" + 0.005*"smile" + 0.005*"uproariously" + 0.005*"war" + 0.005*"state" + 0.005*"only" + 0.004*"excellent" + 0.003*"later"')
  Topic: 0 - Polarity: -0.12
  Topic: 1 - Polarity: -0.0379746835443038
  Topic: 2 - Polarity: 0.1891891891891892
  ```
  
  Topic 2 gathers comments thanking the creator for making the video and, this is reflected in the polarity. Topic 3 contains mostly comments either explaining what happened, correcting the creator, starting a discussion on the topic, or generally just talking plainly about what happened. Upon reading some of the comments in this topic myself, I am unsure why the polarity is not lower. Finally, the first topic, is the trickier one and requires context to be understood. In the video the creator makes a joke about getting a notification about this news while on the toilet (he makes other jokes towards his community later on in the video). This topic mostly gathers jokes, laughs, statements of surprise or ridiculising what happened, references to this one joke "toilet". Importantly, the first two words come from repeated use of the face-with-tears-of-joy emoji. It seems clear that the polarity did not capture sarcasm. The topics are not fully separated since a good portion of users wrote comments containing both jokes and serious statements. Of course, a much more elaborate preprocessing of comments would allow better insight. Although this is just a simple and preliminary analysis, I did find the results interesting. [Try the Code](https://colab.research.google.com/drive/12mlMV-mzG5mUzzXyTOMczoVfBW8NlLNm?usp=sharing).
  

### Why it Resonates with Me
I'm a data journalism enthusiast to the core, and Breaking Italy is one of my go-to for insights. However, I'd watch videos and months later, I'd struggle to find those references buried in the descriptions. So, I put my data science hat on and thought, "Why not blend my passions?". This was a genuine itch I needed to scratch. Hopefully, I have create something practical — something that helps not just me, but anyone who's ever found themselves lost in the vast sea of videos of Breaking Italy.
