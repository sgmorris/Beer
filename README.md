Tidytuesday Great American Beer Festival
================

Background here: [Great American Beer
Festival](https://github.com/rfordatascience/tidytuesday/blob/master/data/2020/2020-10-20/readme.md)

``` r
# Package loads
# for graphs
library(ggplot2)
# cool palette
library(viridis)

# data load
DAT <- readr::read_csv('https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2020/2020-10-20/beer_awards.csv')
```

**Studying the evolution of India Pale Ale**

Extract data rows where categories include the word “India”. Note use of
*ignore.case* to cover variations in caps usage.

``` r
ipa <- DAT[grep("India", DAT$category, ignore.case=TRUE),]
```

Use factor class and *levels* to gather variants on the same beer name
under the same label so for example *India Pale Ale* and *India Pale
Ales* have the same class,

``` r
ipa$category <- factor(ipa$category)
levels(ipa$category) <- c("India Pale Ale",
                          "American-Style India Black Ale",
                          "American-Style India Pale Ale",
                          "Emerging India Pale Ale",
                          "English-Style India Pale Ale",
                          "Experimental India Pale Ale",
                          "Imperial India Pale Ale",
                          "Imperial India Pale Ale",
                          "India Pale Ale",
                          "India Pale Ale",
                          "India Pale Lager",
                          "Juicy or Hazy Imperial India Pale Ale",
                          "Juicy or Hazy Imperial India Pale Ale",
                          "Juicy or Hazy India Pale Ale",
                          "Session India Pale Ale")
```

Cross tabulation of category by year shows the number of awards (gold,
silver, bronze) given out to each category each year. Coerce to data
frame class changes the table to long format with columns for category,
year and count.

Exclude the zeroes and convert the counts to 1 because I just want to
increment the number of categories each year not the number of awards.

``` r
ipa <- data.frame(table(ipa$category, ipa$year))
names(ipa) <- c("category","year","awards")

# exclude zeroes and convert the observations to 1
ipa <- subset(ipa, awards>0)
ipa$awards <- rep(1, nrow(ipa))

# year as numeric means ggplot does a nicer x-axis than with year as a factor
ipa$year <- as.numeric(as.character(ipa$year))

# order categories by first appearance so the legend from top to bottom will match the category appearance from left to right
cat.yr <- sort(tapply(ipa$year, ipa$category, min))
ipa$category <- ordered(ipa$category, names(cat.yr))
```

Bar chart showing the bewildering increase in IPA styles over time.
Millennials clearly to blame.

``` r
p1 <- ggplot(data=ipa, aes(x=year, y=awards, fill=category)) + geom_bar(stat="identity") +
  geom_curve(aes(x=2000, y=3, xend=2018, yend=8), col=2, size=1.5, 
               arrow=arrow(length=unit(0.02,"npc"))) +
  geom_text(aes(x=2005, y=4, label="Rise of the Hipsters"), col=2) + 
  ylab("Number of India Pale Ale categories")

print(p1)
```

![](README_files/figure-gfm/graphic-1.png)<!-- -->

``` r
# export to jpg
jpeg("figure 1.jpg", width=20, height=15, units='cm', res=600)
print(p1)
dev.off()
```

    ## png 
    ##   2
