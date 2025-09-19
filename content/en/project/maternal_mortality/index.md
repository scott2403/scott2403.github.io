---
title: Maternal Mortality
summary: A study on the lifetime risk of maternal mortality.
tags:
  - Data Journalism
  - Data Visualization
date: '2023-08-15T00:00:00Z'

# Optional external URL for project (replaces project detail page).
external_link: ''

image:
  caption: The Lifetime Risk of Maternal Mortality
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
I wanted to study how life expectancy is correlated with maternal mortality: it seems likely that in countries where the maternal mortality is low, then the (female) life expectancy is higher. 

1. For this task, I have taken [Our World in Data's definition of continents](https://ourworldindata.org/grapher/continents-according-to-our-world-in-data) and downloaded it to a CSV file. I had to make sure that I could conciliate the notions of countries/areas between OWID and WHO, where I was taking data from.
2. I downloaded `WPP2022_GEN_F01_DEMOGRAPHIC_INDICATORS_COMPACT_REV1.xlsx"` from [WHO's World Population Prospects](https://population.un.org/wpp/Download/Standard/MostUsed/) to get (Female) Life Expectancy data, dropping unnecessary columns and regions that did not count as countries. This took some parsing, but in the end I redacted the list below of `non_countries`.
```
[
    "WORLD", 
    'Sustainable Development Goal (SDG) regions',
    'Sub-Saharan Africa', 'Northern Africa and Western Asia',
       'Central and Southern Asia', 'Eastern and South-Eastern Asia',
       'Latin America and the Caribbean',
       'Oceania (excluding Australia and New Zealand)',
       'Australia/New Zealand', 'Europe and Northern America',
       'UN development groups', 'More developed regions',
       'Less developed regions', 'Least developed countries',
       'Less developed regions, excluding least developed countries',
       'Less developed regions, excluding China',
       'Land-locked Developing Countries (LLDC)',
       'Small Island Developing States (SIDS)',
       'World Bank income groups', 'High-income countries',
       'Middle-income countries', 'Upper-middle-income countries',
       'Lower-middle-income countries', 'Low-income countries',
       'No income group available', 'Geographic regions', 'AFRICA',
        'Eastern Africa', 'Middle Africa', 'Northern Africa',
        'Southern Africa', 'Western Africa', 'ASIA', 'Central Asia',
    'Eastern Asia', 'Southern Asia', 'South-Eastern Asia',
    'Western Asia', 'EUROPE', 'Eastern Europe', 'Northern Europe',
    'Southern Europe', 'Western Europe', 'LATIN AMERICA AND THE CARIBBEAN', 'Caribbean',
    'OCEANIA', 'Central America', 'South America', 'NORTHERN AMERICA']
```
3. I have then downloaded `HOMortalityDatabase_Map_Maternal conditions_28th March 2024 17_52.csv"` from WHO's mortality database, and created a dataframe with `Sex==Female` all age groups, and the raw mortality numbers by country and year. 
4. I then had to make sure that the countries agreed between OWID and WHO (even if just for a data wrangling exercise). There were a few annoying things like `Réunion` vs `Reunion` or `Türkiye` vs `Turkiye` which took me a while to spot. 
5. Finally, I have computed the (raw) Maternal Mortality rate and plotted it (for the year 2014, since it had the most datapoints) against Female life expectancy. 

From the plot above we can see quite clearly that countries with low MMR tend to have higher Female life expectancy, as expected! You can reproduce the code [here](https://colab.research.google.com/drive/1RO9nxdAobLjNdbVxpnwbgWU2FwJsB7Cp?usp=sharing).
