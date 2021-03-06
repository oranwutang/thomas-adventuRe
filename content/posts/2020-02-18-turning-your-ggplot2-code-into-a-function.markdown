---
title: Turning your ggplot2 code into a function
author: Thomas Neitmann
date: '2020-02-18'
slug: turning-your-ggplot2-code-into-a-function
categories:
  - R
  - bytesized
tags:
  - ggplot2
  - datavisualization
  - rlang
toc: no
images: ~
---




If you find yourself repeatedly writing the same `ggplot2` code to create a data visualization in `R`, then it's time to put your code into a function.

You may start out with an implementation similar to this one.


```r
library(ggplot2)
theme_set(ggcharts::theme_hermit(grid = "XY"))
data("mtcars")

scatter_plot <- function(data, x, y) {
  ggplot(data, aes(x, y)) +
    geom_point(color = "yellow")
}
```


That won't work though.


```r
scatter_plot(mtcars, hp, mpg)
```

```
## Error in FUN(X[[i]], ...): object 'hp' not found
```

If you call this function, `R` will look for a variable called `hp` rather than looking for a column with that name inside the data frame you passed as the first argument.

So, maybe it works when putting the column names in quotes?


```r
scatter_plot(mtcars, "hp", "mpg")
```

<img src="/posts/2020-02-18-turning-your-ggplot2-code-into-a-function_files/figure-html/unnamed-chunk-4-1.png" width="672" />


Well, no error this time but that most likely did not produce what you expected.

<a target="_blank"  href="https://www.amazon.com/gp/product/331924275X/ref=as_li_tl?ie=UTF8&camp=1789&creative=9325&creativeASIN=331924275X&linkCode=as2&tag=07075-20&linkId=77ed1cd1e91cea9ef42c6b604b8d85e5"><img border="0" src="//ws-na.amazon-adsystem.com/widgets/q?_encoding=UTF8&MarketPlace=US&ASIN=331924275X&ServiceVersion=20070822&ID=AsinImage&WS=1&Format=_SL250_&tag=07075-20" ></a><img src="//ir-na.amazon-adsystem.com/e/ir?t=07075-20&l=am2&o=1&a=331924275X" width="1" height="1" border="0" alt="" style="border:none !important; margin:0px !important;" />

The key to making this work is to tell `R`  somehow that it should look for the `x` and `y` arguments inside data. How can you achieve this? Using `{{ }}` (speak curly-curly) from the `rlang` package.


```r
scatter_plot2 <- function(data, x, y) {
  ggplot(data, aes({{x}}, {{y}})) +
    geom_point(color = "yellow")
}
scatter_plot2(mtcars, hp, mpg)
```

<img src="/posts/2020-02-18-turning-your-ggplot2-code-into-a-function_files/figure-html/unnamed-chunk-5-1.png" width="672" />


There you have it: that's how you can create your own custom plotting function on top of `ggplot2`.

Want to see the power of custom plotting functions in action? Make sure to check out my [`ggcharts`](https://github.com/thomas-neitmann/ggcharts) package.
