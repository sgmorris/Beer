Tidytuesday Great American Beer Festival
================

``` r
# categories including the word "India"
ipa <- DAT[grep("India", DAT$category),]
# use factor classes to gather similar names eg "India pale ale" anf "india pale ales"
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
  
# number of unique categories by year eliminates duplicates with award type
ipa <- data.frame(table(ipa$category, ipa$year))
names(ipa) <- c("category","year","awards")

ipa <- subset(ipa, awards>0)
ipa$awards <- rep(1, nrow(ipa))

ipa$year <- as.numeric(as.character(ipa$year))

# order categories by first apperance
cat.yr <- sort(tapply(ipa$year, ipa$category, min))
ipa$category <- ordered(ipa$category, names(cat.yr))

ggplot(data=ipa, aes(x=year, y=awards, fill=category)) + geom_bar(stat="identity") +
  geom_segment(aes(x=2000, y=3, xend=2018, yend=7), col="blue") +
  geom_text(aes(x=2005, y=6, label="Rise of the Hipsters")) + 
  ylab("Number of India ale categories") + ggtitle("IPA used to be IPA")
```

![](README_files/figure-gfm/ipa-1.png)<!-- -->
