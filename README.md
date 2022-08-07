入門統計解析 演習問題の R による解答例
================
obcat

## 概要

[入門統計解析
(新世社)](https://www.saiensu.co.jp/search/?isbn=978-4-88384-140-0&y=2009)
演習問題の R による解答例です。

-   解答は間違っているかもしれません。

-   できるだけ tidyverse 圏で頑張りました:

    ::: {.cell}

    ``` r
    library(tidyverse)
    ```

    :::

-   [公式のサポートページ](https://www.saiensu.co.jp/search/?isbn=978-4-88384-140-0&y=2009#support)
    よりダウンロードしたデータソースを利用しています:

    ::: {.cell}

    ``` r
    if (!xfun::file_exists("data/data.xlsx")) {
      curl::curl_download(
        "https://www.saiensu.co.jp/book_support/978-4-88384-140-0/data.xlsx",
        "data/data.xlsx"
      )
    }
    ```

    :::

## 問題 2.1

### 1

最小階級の下限を `700` 円、最大階級の上限を `3500` 円とし、階級幅を
`400` とする:

``` r
階級境界 <- seq(from = 700, to = 3500, by = 400)
階級下限 <- 階級境界[-length(階級境界)]
階級上限 <- 階級境界[-1]
階級名 <- str_c(階級下限, 階級上限, sep = " ～ ")
階級値 <- (階級下限 + 階級上限) / 2
```

度数分布表は:

``` r
表2.1 <- readxl::read_excel("data/data.xlsx",
                               sheet = "表2.1",
                               range = "A2:C49")
度数分布表 <- 表2.1 %>% 
  group_by(
    階級   = cut(`家賃(円）`, breaks = 階級境界, right = FALSE, labels = 階級名),
    階級値 = cut(`家賃(円）`, breaks = 階級境界, right = FALSE, labels = 階級値),
  ) %>% 
  summarise(度数 = n(), .groups = "drop") %>% 
  mutate(
    累積度数     = cumsum(度数),
    相対度数     = 度数 / sum(度数),
    累積相対度数 = cumsum(相対度数),
  )
度数分布表
```

    # A tibble: 7 × 6
      階級         階級値  度数 累積度数 相対度数 累積相対度数
      <fct>        <fct>  <int>    <int>    <dbl>        <dbl>
    1 700 ～ 1100  900       16       16   0.340         0.340
    2 1100 ～ 1500 1300      19       35   0.404         0.745
    3 1500 ～ 1900 1700       2       37   0.0426        0.787
    4 1900 ～ 2300 2100       5       42   0.106         0.894
    5 2300 ～ 2700 2500       2       44   0.0426        0.936
    6 2700 ～ 3100 2900       1       45   0.0213        0.957
    7 3100 ～ 3500 3300       2       47   0.0426        1    

したがって、モードは:

``` r
モード <- 度数分布表 %>% slice_max(度数) %>% pull(階級値) %>% as.character() %>% as.integer()
モード
```

    [1] 1300
