# Chart: Histogram {#histo}

![](images/banners/banner_histogram.png)

## Overview

This section covers how to make histograms.

## tl;dr
Gimme a full-fledged example!

Here's an application of histograms that looks at how the beaks of Galapagos finches changed due to external factors:

<img src="histogram_files/figure-html/tldr-finch-example-1.png" width="672" />

And here's the code:

```r
library(Sleuth3) # data
library(ggplot2) # plotting

# load data
finches <- Sleuth3::case0201
# finch histograms by year with overlayed density curves
ggplot(finches, aes(x = Depth, y = ..density..)) + 
  # plotting
  geom_histogram(bins = 20, colour = "#80593D", fill = "#9FC29F", boundary = 0) +
  geom_density(color = "#3D6480") + 
  facet_wrap(~Year) +
  # formatting
  ggtitle("Severe Drought Led to Finches with Bigger Chompers",
          subtitle = "Beak Depth Density of Galapagos Finches by Year") +
  labs(x = "Beak Depth (mm)", caption = "Source: Sleuth3::case0201") +
  theme(plot.title = element_text(face = "bold")) +
  theme(plot.subtitle = element_text(face = "bold", color = "grey35")) +
  theme(plot.caption = element_text(color = "grey68"))
```

For more info on this dataset, type `?Sleuth3::case0201` into the console.

## Simple Examples
Whoa whoa whoa! Much simpler please!

Let's use a very simple dataset:

```r
# store data
x <- c(50, 51, 53, 55, 56, 60, 65, 65, 68)
```

### Histogram using Base R

```r
# plot data
hist(x, col = "lightblue", main = "Base R Histogram of x")
```

<img src="histogram_files/figure-html/base-r-hist-1.png" width="672" />

For the Base R histogram, it's advantages are in it's ease to setup. In truth, all you need to plot the data `x` in question is `hist(x)`, but we included a little color and a title to make it more presentable. 

Full documentation on `hist()` can be found [here](https://www.rdocumentation.org/packages/graphics/versions/3.5.0/topics/hist){target="_blank"}

### Histogram using ggplot2

```r
# import ggplot
library(ggplot2)
# must store data as dataframe
df <- data.frame(x)

# plot data
ggplot(df, aes(x)) +
  geom_histogram(color = "grey", fill = "lightBlue", 
                 binwidth = 5, center = 52.5) +
  ggtitle("ggplot2 histogram of x")
```

<img src="histogram_files/figure-html/ggplot-hist-1.png" width="672" />

The ggplot version is a little more complicated on the surface, but you get more power and control as a result. **Note**: as shown above, ggplot expects a dataframe, so if you are getting an error where "R doesn't know what to do" like this:

![ggplot dataframe error](images/ggplot_df_error.png)

make sure you are using a dataframe.

## When to use
Use a histogram to show the distribution of *one continuous variable*. The y-scale can be represented in a variety of ways to express different results:

*   **Count**: Number of points that fall in each bin
*   **Relative frequency**: (**Count**) / (Total Number of datapoints)
*   **Cumulative Frequency**: Accumulation of all previous **Relative frequencies**
*   **Density**: (**Relative Frequency**) / (binwidth)

## Considerations

### Bin Boundaries
Be mindful of the boundaries of the bins and whether a point will fall into the left or right bin if it is on a boundary.

```r
# format layout
op <- par(mfrow = c(1, 2), las = 1)

# right closed
hist(x, col = "lightblue", ylim = c(0, 4),
     xlab = "right closed ex. (55, 60]", font.lab = 2)
# right open
hist(x, col = "lightblue", right = FALSE, ylim = c(0, 4),
     xlab = "right open ex. [55, 60)", font.lab = 2)
```

<img src="histogram_files/figure-html/bin-boundaries-1.png" width="672" />

### Bin Number
The default bin number of 30 in ggplot2 is not always ideal, so consider altering it if things are looking strange. You can specify the width explicitly with `binwidth` or provide the desired number of bins with `bins`.

```r
# default...note the pop-up about default bin number
ggplot(finches, aes(x = Depth)) +
  geom_histogram() +
  ggtitle("Default with pop-up about bin number")
```

```
## `stat_bin()` using `bins = 30`. Pick better value with `binwidth`.
```

<img src="histogram_files/figure-html/unnamed-chunk-1-1.png" width="672" />

Here are examples of changing the bins using the two ways described above:

```r
# using binwidth
p1 <- ggplot(finches, aes(x = Depth)) +
  geom_histogram(binwidth = 0.5, boundary = 6) +
  ggtitle("Changed binwidth value")
# using bins
p2 <- ggplot(finches, aes(x = Depth)) +
  geom_histogram(bins = 48, boundary = 6) +
  ggtitle("Changed bins value")

# format plot layout
library(gridExtra)
grid.arrange(p1, p2, ncol = 2)
```

<img src="histogram_files/figure-html/fixed-histograms-binwidth-1.png" width="672" />


### Bin Alignment
Make sure the axes reflect the true boundaries of the histogram. You can use `boundary` to specify the endpoint of any bin or `center` to specify the center of any bin. `ggplot2` will be able to calculate where to place the rest of the bins (Also, notice that when the boundary was changed, the number of bins got smaller by one. This is because by default the bins are centered and go over/under the range of the data.)

```r
df <- data.frame(x)

# default alignment
ggplot(df, aes(x)) +
  geom_histogram(binwidth = 5,
                 fill = "lightBlue", col = "black") +
  ggtitle("Default Bin Alignment")
```

<img src="histogram_files/figure-html/unnamed-chunk-2-1.png" width="672" />


```r
# specify alignment with boundary
p3 <- ggplot(df, aes(x)) +
  geom_histogram(binwidth = 5, boundary = 60,
                 fill = "lightBlue", col = "black") +
  ggtitle("Bin Alignment Using boundary")

# specify alignment with center
p4 <- ggplot(df, aes(x)) +
  geom_histogram(binwidth = 5, center = 67.5,
                 fill = "lightBlue", col = "black") +
  ggtitle("Bin Alignment Using center")

# format layout
library(gridExtra)
grid.arrange(p3, p4, ncol = 2)
```

<img src="histogram_files/figure-html/alignment-fix-1.png" width="672" />

**Note**: Don't use both `boundary` *and* `center` for bin alignment. Just pick one.

## Theory

*   For more info about histograms and continuous variables, check out [Chapter 3](http://www.gradaanwr.net/content/03-examining-continuous-variables/){target="_blank"} of the textbook. 

## External Resources

- [DataCamp ggplot2 Histograms Exercise](https://campus.datacamp.com/courses/data-visualization-with-ggplot2-1/chapter-4-geometries?ex=5){target="_blank"}: Simple interactive example of histograms with ggplot2
- [DataCamp Histogram with Basic R](https://www.datacamp.com/community/tutorials/make-histogram-basic-r){target="_blank"}: "Tutorial for new R users whom need an accessible and easy-to-understand resource on how to create their own histogram with basic R.
" 'Nuff said.
- [DataCamp Histogram with ggplot2](https://www.datacamp.com/community/tutorials/make-histogram-ggplot2){target="_blank"}: Great article on making histograms with ggplot2.
- [hist documentation](https://www.rdocumentation.org/packages/graphics/versions/3.5.0/topics/hist){target="_blank"}: base R histogram documentation page.
- [ggplot2 cheatsheet](https://www.rstudio.com/wp-content/uploads/2015/03/ggplot2-cheatsheet.pdf){target="_blank"}: Always good to have close by.



