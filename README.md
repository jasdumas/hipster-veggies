
<!-- README.md is generated from README.Rmd. Please edit that file -->
hipster-veggies
===============

Find Select Trending Vegetables from Twitter :carrot:

Analysis
========

Going to use the `rtweet` library and some assorted `tidyverse` things.

``` r
library(rtweet)
library(tidyverse)

twitter_token <- create_token(
app = Sys.getenv("appname"),
consumer_key = Sys.getenv("twitter_key"),
consumer_secret = Sys.getenv("twitter_secret"))
```

Searching for the top most recent tweets for each vegetable from the article: [7 Hipster Vegetables Most Likely to Dethrone Kale](https://spoonuniversity.com/lifestyle/7-hipster-vegetables-most-likely-to-dethrone-kale). The documentation for the query arguement to search for more words by using the 'OR' operator did not work, so multiple searches we ran.

``` r

radish_tweets <- search_tweets(q="radish", 
                               n = 500, 
                               include_rts = FALSE, retryonratelimit = TRUE, lang = "en") 

cauliflower_tweets <- search_tweets(q="cauliflower", 
                                    n = 500, 
                                    include_rts = FALSE, retryonratelimit = TRUE, lang = "en")

turnip_tweets <- search_tweets(q="turnip", 
                                    n = 500, 
                                    include_rts = FALSE, retryonratelimit = TRUE, lang = "en")

jimica_tweets <- search_tweets(q="jimica", 
                                    n = 500, 
                                    include_rts = FALSE, retryonratelimit = TRUE, lang = "en")

rc_tweets <- search_tweets(q="'rainbow chard'", 
                               n = 500, 
                               include_rts = FALSE, retryonratelimit = TRUE, lang = "en") 


bs_tweets <- search_tweets(q="'brussels sprout'", 
                                    n = 500, 
                                    include_rts = FALSE, retryonratelimit = TRUE, lang = "en") 
# kabocha squash
ks_tweets <- search_tweets(q="kabocha", 
                                    n = 500, 
                                    include_rts = FALSE, retryonratelimit = TRUE)

ks_tweets <- dplyr::filter(ks_tweets, lang == "en")
```

Let's combine these data sets into one for graphing while preserving the original vegetable type by creating an additional column.

``` r
bs_tweets$vegetable <- "brussels sprout"
cauliflower_tweets$vegetable <- "cauliflower"
jimica_tweets$vegetable <- "jimica"
ks_tweets$vegetable <- "kabocha squash"
radish_tweets$vegetable <- "radish"
rc_tweets$vegetable <- "rainbow chard"
turnip_tweets$vegetable <- "turnip"

all_veggies <- rbind(bs_tweets, cauliflower_tweets, jimica_tweets, ks_tweets, radish_tweets, rc_tweets, turnip_tweets)
```

``` r
all_veggies %>%  group_by(created_at, vegetable) %>% 
                  summarise(n = n() ) %>% 
            ggplot(., aes(x = created_at)) +
                geom_freqpoly(aes(color = vegetable)) +
                scale_color_brewer(palette = "Set1") +
                #geom_label() +
                theme_minimal() +
                theme(plot.title = ggplot2::element_text(face = "bold")) +
                labs( x = NULL, title = "Frequency of Tweets Mentioning Hipster Vegetables", subtitle = "from the past 9 days", caption = "\nSource: Data collected from Twitter's REST API via rtweet"
  )
```

![](README-unnamed-chunk-5-1.png)
