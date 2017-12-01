
[hipster-veggies](https://jasdumas.github.io/hipster-veggies/)
==============================================================

Find Select Trending Vegetables from Twitter 🥕

![](https://media.giphy.com/media/mnnJUiakh7qrS/giphy.gif)

Analysis
--------

Going to use the `rtweet` library and some assorted `tidyverse` things.

``` r
library(rtweet) # devtools::install_github("mkearney/rtweet")
library(tidyverse)

# I persisted these credientials in my ~/.Rprofile
twitter_token <- create_token(
app = Sys.getenv("appname"),
consumer_key = Sys.getenv("twitter_key"),
consumer_secret = Sys.getenv("twitter_secret"))
```

Searching for the top most recent tweets for each vegetable from the article: [7 Hipster Vegetables Most Likely to Dethrone Kale](https://spoonuniversity.com/lifestyle/7-hipster-vegetables-most-likely-to-dethrone-kale). The documentation for the query arguement to search for more words by using the 'OR' operator did not work, so multiple searches were ran.

``` r

radish_tweets <- search_tweets(q="radish", 
                               n = 1000, 
                               include_rts = FALSE, retryonratelimit = TRUE, lang = "en") 

cauliflower_tweets <- search_tweets(q="cauliflower", 
                                    n = 1000, 
                                    include_rts = FALSE, retryonratelimit = TRUE, lang = "en")

turnip_tweets <- search_tweets(q="turnip", 
                                    n = 1000, 
                                    include_rts = FALSE, retryonratelimit = TRUE, lang = "en")

jimica_tweets <- search_tweets(q="jicama", 
                                    n = 1000, 
                                    include_rts = FALSE, retryonratelimit = TRUE, lang = "en")

rc_tweets <- search_tweets(q="rainbow chard", 
                               n = 1000, 
                               include_rts = FALSE, retryonratelimit = TRUE, lang = "en") 


bs_tweets <- search_tweets(q="brussels sprout", 
                                    n = 1000, 
                                    include_rts = FALSE, retryonratelimit = TRUE, lang = "en") 


ks_tweets <- search_tweets(q="kabocha squash", 
                                    n = 1000, 
                                    include_rts = FALSE, retryonratelimit = TRUE, lang = "en")

#ks_tweets <- dplyr::filter(ks_tweets, lang == "en")
```

A preview of tweets about rainbow chard.

``` r
head(rc_tweets$text)
#> [1] "Vegan Caes sitch Rainbow chard Brussels and Napa cabbage w/ pine nut Parm recipe adopted from @thefirstmess https://t.co/YG7Ca264wC https://t.co/5cDqg1VRpR"                                                                                                                               
#> [2] "Vegan caes sitch with rainbow chard Napa cabbage Brussels and smoky chic peas a la @thefirstmess #pinenutparm #yesbread #yesveggies https://t.co/r55ayJgUy9 https://t.co/wqGBKDl9uG"                                                                                                       
#> [3] "#comfort grilled tri tip &amp; eggplant w/ arugula chimichurri sauce &amp; happy boy farms rainbow chard!! john_dickman _hawko @marciadorsey https://t.co/3v0clvqDUt"                                                                                                                      
#> [4] "Did you know that rainbow chard is a mix of chard varieties, not just one plant? Visit the market today for fresh, organically grown rainbow chard from Ground Stew Farms. \U0001f308 https://t.co/O3rAQ0m0i0"                                                                             
#> [5] "#photobomb by rainbow Swiss chard! Blue curly kale growing with chard,  basil &amp; more... all… https://t.co/MwM4xeaENo"                                                                                                                                                                  
#> [6] "@JBGorganic will have tomatoes, fresh lettuce, beet bunches, broccoli, green cabbage, napa cabbage, carrot bunches, fennel, a bouquet of radishes, sweet potatoes, butternut squash, turnips, cilantro, dill, parsley, arugula, bok choy, braising mixed greens, rainbow chard &amp; more!"
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

Let's plot the data to observe any trends.

``` r
gg_veggies <- all_veggies %>%  group_by(created_at, vegetable) %>% 
                  summarise(n = n() ) %>% 
            ggplot(., aes(x = created_at)) +
                geom_freqpoly(aes(color = vegetable)) +
                scale_color_brewer(palette = "Set1") +
                theme_minimal() +
                theme(plot.title = element_text(face = "bold"), legend.title = element_blank()) +
                labs( x = NULL, y = "count (log scale)", title = "Frequency of Tweets Mentioning Hipster Vegetables", subtitle = "from the past 9 days", caption = "\nSource: Data collected from Twitter's REST API via rtweet"
  ) + scale_y_log10(breaks = c(1, 10, 100, 200, 300, 400))

gg_veggies
```

![](README-veggies-1.png)

fin 🎉

Extra
-----

*Here are some other articles about **Hipster** Vegetables*:

1.  [The 22 Most Hipster Foods On The Planet - HuffPost](https://www.huffingtonpost.com/2014/04/15/hipster-food_n_5146632.html)

2.  [What Will Be the Next Hipster Vegetable?](https://www.pastemagazine.com/articles/2015/09/what-will-be-the-next-hipster-vegetable.html)

Contact Me
----------

Jasmine Dumas (@jasdumas) [twitter](https://twitter.com/jasdumas), [jasdumas.github.io](http://jasdumas.github.io/)
