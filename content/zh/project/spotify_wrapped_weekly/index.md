---
title: Spotify Wrapped Weekly
summary: Real-time Data Visualization via the Spotify API.
tags:
  - Spotify API
  - Flask
  - Heroku
  - HTML
  - Spotify
date: '2023-08-15T00:00:00Z'

# Optional external URL for project (replaces project detail page).
external_link: ''

image:
  caption: AI Generated Black Spotify Logo
  focal_point: Smart
  width: 20
  height: 30

links:
  - icon: github
    icon_pack: fab
    name: GitHub
    url: https://github.com/MauroCE/Spotify-Wrapped-Weekly
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
I had been meaning to learn how to use the Spotify API for a while so I embarked on creating my very own [Weekly Spotify Wrapped](https://spotify-mood-ring-2a2e81fbe0b0.herokuapp.com). 

<div style="text-align: center;">
<button class="button" align="center" onclick="window.location.href='https://spotify-mood-ring-2a2e81fbe0b0.herokuapp.com'">Check out this Week's Spotify Wrapped</button>
</div>
<!-- <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/cf-gotham-fonts@1.0.0/gotham-font.min.css">
<div class="button-container">
    <a class="button" href='https://spotify-mood-ring-2a2e81fbe0b0.herokuapp.com'>
        Check out this Week's Spotify Wrapped
    </a>
</div> -->
<p></p>
Here's a summary of what I did:

- I used my `CLIENT_ID` and `CLIENT_SECRET` to get an access token with [Authorization Code](https://developer.spotify.com/documentation/web-api/tutorials/code-flow) grant type. To avoid having to repeatedly authorize the application, I also use a [refresh token](https://developer.spotify.com/documentation/ios/concepts/token-swap-and-refresh).
- I used token to grab recently played songs that have been played in the current week. Spotify currently only stores the last 50 recently played songs, meaning that using `'next'` cursor, or a while loop with `'after'` or `'before'` would not lead to any more songs. 
    - To make my request cleaner I have used field filtering:
      ```
      url = "https://api.spotify.com/v1/me/player/recently-played"
      timestamp_ms = int(starting_date.timestamp()) * 1000
      query = f"?limit={limit}&after={timestamp_ms}"
      query += "&fields=track.name,track.artists.name,track.artists.id,played_at"
      query_url = url + query
      ```
    - I have implemented Caching to make the Dashboard more responsive and faster to load. In particular, I am using the `Flask` `filesystem` Cache, which is a bit of an overkill given that we only have access to $50$ songs. I have set a timeout of a day.
    - Wrote a decorator to handle API errors when `GET`ting data more elegantly.
    - The function `group_songs_by_artists` now stores artist ids and their genres into a file for already requested artists. This means that as time goes on, this app will require fewer requests to extract the artist genres, since these are not accessible from `recently_played`. 
- I have then used this data and [Chart.js](https://www.chartjs.org/) to produce the following three visualizations:
    1. **Pie Chart** showing which artists I have listened to this week.
    2. **Bar Chart** showing at what time of the day I have mostly listened to songs.
    3. **Bar Chart** showing the genres of the songs.
    
My aim in the future will be to implement a mood ring, by inferring the mood from songs either based on lyrics or metrics such as tempo and cadence. A redacted copy of the code is available in my [GitHub Repository](https://github.com/MauroCE/Spotify-Wrapped-Weekly).

Another plan for the future is to create a cronjob that pulls recently played songs at the end of each day, and stores them in a database or file system. This way one can create a rolling-window of recently played songs and actually show data for the whole week. 
