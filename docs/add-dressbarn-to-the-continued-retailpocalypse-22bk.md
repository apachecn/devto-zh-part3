# 将 Dressbarn 添加到 Continued Retailpocalypse

> 原文：<https://dev.to/hrbrmstr/add-dressbarn-to-the-continued-retailpocalypse-22bk>

在之前，我已经[谈论过零售困局，今天早上，当我打开浏览器时，我看到了](https://rud.is/b/2017/03/19/exploring-2017-retail-store-closings-with-r/)[关于 Dressbarn 关闭所有 650 家商店的消息](https://news.google.com/stories/CAAqSQgKIkNDQklTTERvSmMzUnZjbmt0TXpZd1NoOGFIV1JqYlhaWGFrUkhla3BzZFhwcVRYVkxMV2hpV2w5dmJteHJXVGhOS0FBUAE?q=dressbarn&lr=English&hl=en-US&gl=US&ceid=US:en)。

我发了一些图片和数据，但不是每个人都在 Twitter 上，所以我只是在这里发布一个博客简介，并附上代码和数据链接。

代码如下，在[https://paste . Sr . ht/~ hrbrmstr/af 6 da 1 af 0314426255 c 65 BC 2 fc 254 E0 abb 2190 c 3](https://paste.sr.ht/~hrbrmstr/af6da1af0314426255c65bc2fc254e0abb2190c3)。

数据在[https://rud.is/dl/dressbarn-locations.json.gz](https://rud.is/dl/dressbarn-locations.json.gz)。

图片在代码下面的图库中。

```
library(rvest)
library(stringi)
library(urltools)
library(worldtilegrid) # install from sh/gl/gh or just remove the theme_enhange_wtg() calls
library(statebins)
library(tidyverse)

# this is the dressbarn locations directory page
pg <- read_html("https://locations.dressbarn.com/")

# this is the selector to get the main links
html_nodes(pg, "a.Directory-listLink") %>% 
  html_attr("href") -> locs

# PRE-NOTE
# No sleep() code (I looked at the web site, saw how many self-requests it makes for all DB
# resources and concluded that link scrapes + full page captures would not be burdensome
# plus they're going out of business)

# basic idea here is to get all the main state location pages
# some states only have one store so the link goes right to that so handle that condition
# for ones with multiple stores get all the links on the state index page
# for links on state index page that have multiple stores in one area,
# grab all those; then, concatenate all the final target store links into one 
# character vector.

keep(locs, ~nchar(.x) == 2) %>% 
  sprintf("https://locations.dressbarn.com/%s", .) %>% # state has multiple listings
  map(
    ~read_html(.x) %>% 
      html_nodes("a.Directory-listLink") %>% 
      html_attr("href") %>% 
      sprintf("https://locations.dressbarn.com/%s", .)
  ) %>% 
  append(
    keep(locs, ~nchar(.x) > 2) %>% sprintf("https://locations.dressbarn.com/%s", .) # state has one store
  ) %>% 
  flatten_chr() %>% 
  map_if(
    ~stri_count_fixed(.x, "/") == 4, # 4 URL parts == there's another listing page layer
    ~read_html(.x) %>% 
      html_nodes("a.Teaser-titleLink") %>% 
      html_attr("href") %>% 
      stri_replace_first_fixed("../", "") %>% 
      sprintf("https://locations.dressbarn.com/%s", .)
  ) %>% 
  flatten_chr() -> listings

# make a tibble with the HTML source for the final store location pages
# so we don't end up doing multiple retrievals

tibble(
  listing = listings,
  html_src = map_chr(listings, ~httr::GET(.x) %>% httr::content(as = "text"))
) -> dress_barn

# save off our work in the event we have a (non-R-crashing) issue
tf <- tempfile(fileext = ".rds")
print(tf)
saveRDS(dress_barn, tf) 

# now, get data from the pages
#
# first, turn all the character vectors into something we can get HTML nodes from
#
# dressbarn web folks handliy put an "uber" link on each page so we get lon/lat for free in that URL
# they also handily used an <address> semantic tag in the proper PostalAddress schema format
# so we can get locality and actual address, too
mutate(
  dress_barn,
  parsed = map(html_src, read_html),
  uber_link = 
    map_chr(
      parsed, ~html_nodes(.x, xpath=".//a[contains(@href, 'uber')]") %>% 
        html_attr("href") 
    ), 
  locality = map_chr(
    parsed, ~html_node(.x, xpath=".//address/meta[@itemprop = 'addressLocality']") %>% 
      html_attr("content")
  ),
  address = map_chr(
    parsed, ~html_node(.x, xpath=".//address/meta[@itemprop = 'streetAddress']") %>% 
      html_attr("content")
  ),
  state = stri_match_first_regex(
    dress_barn$listing, 
    "https://locations.dressbarn.com/([[:alpha:]]+)/.*$"
  )[,2]
) %>% 
  bind_cols(
    param_get(.$uber_link, c("dropoff%5Blatitude%5D", "dropoff%5Blongitude%5D")) %>% 
      as_tibble() %>% 
      set_names(c("lat", "lon")) %>%
      mutate_all(as.double)
  ) -> dress_barn

# save off our hard work with the HTML source so we can do more later if need be
select(dress_barn, -parsed) %>% 
  saveRDS("~/Data/dressbarn-with-src.rds")

# save off something others will want
select(dress_barn, -parsed, -html_src, -listing) %>% 
  jsonlite::toJSON() %>% 
  write_lines("~/Data/dressbarn-locations.json.gz")

# simple map
ggplot(dress_barn, aes(lon, lat)) + 
  geom_jitter(size = 0.25, color = ft_cols$yellow, alpha = 1/2) +
  coord_map("polyconic") +
  labs(
    title = "Locations of U.S. Dressbarn Stores",
    subtitle = "All 650 locations closing",
    caption = "Source: Dressbarn HTML store listings;\nData: <https://rud.is/dl/dressbarn-locations.json.gz> via @hrbrmstr"
  ) +
  theme_ft_rc(grid="") +
  theme_enhance_wtg()

unlink(tf) # cleanup 

count(dress_barn, state) %>% 
  left_join(tibble(name = state.name, state = tolower(state.abb))) %>% 
  left_join(usmap::statepop, by = c("name"="full")) %>% 
  mutate(per_capita = (n/pop_2015) * 1000000) %>% 
  arrange(desc(per_capita)) %>% 
  select(name, n, per_capita) %>% 
  arrange(desc(per_capita)) %>% 
  complete(name = state.name) %>% 
  statebins(state_col = "name", value_col = "per_capita", ) +
  scale_fill_r7c("Closing\nper-capita") +
  labs(title = "Dressbarn State per-capita closings") +
  theme_ipsum_rc(grid="") +
  theme_enhance_wtg() 
```

Enter fullscreen mode Exit fullscreen mode

[![](img/ee8458d2072d87ab0212a3124fd4fdf8.png)](https://rud.is/b/2019/05/21/add-dressbarn-to-the-continued-retailpocalypse/dressbarn-closings-map/)[![](img/2c2d666b653e57a16707eab55a55d038.png)](https://rud.is/b/2019/05/21/add-dressbarn-to-the-continued-retailpocalypse/dressbarn-per-capita-heatmap/)[![Dressbarn closings visualizations](img/544548c147e97ed56fdc39997d986c69.png)](https://rud.is/b/2019/05/21/add-dressbarn-to-the-continued-retailpocalypse/dressbarn-top-10-per-capita/)