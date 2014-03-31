R TUTORIAL (III)
========================================================


When data you need is from different sources, you will nedd to have a clear integration strategy. For this section we will collect some data from the web, clean them and combine them into a single file.

As I will need to **scrap** many data sites, it is important to create a procedure that improves readability and makes coding more efficient. This function will look like this:


```r
scrapData <- function(linkWhere,whichTable,toFactor=FALSE) 
  {  require(XML) 
     dirtyTable <- getNodeSet(htmlParse(linkWhere),"//table") [[whichTable]]
     tableToReturn <- readHTMLTable(dirtyTable,   #the generated above
                                    header=TRUE,  #first line is the header
                                    trim=TRUE,    #eliminate leading and trailing white spaces in cells
                                    stringsAsFactors = toFactor #do not convert strings to factors
                                    ) }
```


I took care to organise the input parameters in such a way that it is flexible to the way the data in the web page is presented. As it is clear from the function, we always need to provide a *link* and to indicate *which table* to scrap, and optionally inform if we need to convert strings to factors.

Suppose that in this tutorial I want to organise some data at a country level by integrating some composite indexes. In this case, I will use the Indices of Freedom :
<img src="http://i.imgur.com/hm7jOcA.png?1"height="500" width="800" align=center> 

And the Global terrorism index:  
<img src="http://i.imgur.com/I5BwHJN.png"height="500" width="500" align=center> 

Since I think it might be neccessary some information on the location of the countries, I will get the region of the country from **cloford.com**:

<img src="http://i.imgur.com/uJTk7Qx.png"height="500" width="800" align=center> 

So, these are links needed:

```r
freedomLink = "http://en.wikipedia.org/wiki/List_of_Indices_of_Freedom"
terrorismLink = "http://en.wikipedia.org/wiki/Global_Terrorism_Index"
regionsLink = "http://cloford.com/resources/codes/"
```


Next, I apply the function to them:

```r
Freedom <- scrapData(freedomLink, 3)
```

```
## Loading required package: XML
```

```r
Terrorism <- scrapData(terrorismLink, 1)
Regions <- scrapData(regionsLink, 5)
```


I also have the Human Development Index in a web repository in a simple csv format:


```r
library(RCurl)
data <- getURL("https://raw.github.com/MAGALLANESJoseManuel/SocialScienceDataTools/master/TemplatesR/hdi.csv")
HDI <- read.csv(text = data)
```


I combine the 3 data sets into *dataCountries*. For that, I have identified that they all have a column named "Country": 

```r
dataCountries <- merge(Regions, Freedom, by = "Country")
dataCountries <- merge(dataCountries, Terrorism, by = "Country")
dataCountries <- merge(dataCountries, HDI, by = "Country")
```

Let's see what is the result so far:

```r
str(dataCountries)
```

```
## 'data.frame':	147 obs. of  17 variables:
##  $ Country                       : chr  "Afghanistan" "Albania" "Algeria" "Angola" ...
##  $ Continent                     : chr  "Asia" "Europe" "Africa" "Africa" ...
##  $ Region                        : chr  "South Asia" "South East Europe" "Northern Africa" "Southern Africa" ...
##  $ Capital                       : chr  "Kabul" "Tirana" "Algiers" "Luanda" ...
##  $ FIPS                          : chr  "AF" "AL" "AG" "AO" ...
##  $ ISO (2)                       : chr  "AF" "AL" "DZ" "AO" ...
##  $ ISO (3)                       : chr  "AFG" "ALB" "DZA" "AGO" ...
##  $ ISO (No)                      : chr  "004" "008" "012" "024" ...
##  $ Internet                      : chr  "AF" "AL" "DZ" "AO" ...
##  $ Note                          : chr  "Â" "Â" "Â" "Â" ...
##  $ Freedom in the World 2014     : chr  "5 not free" "3 partly free" "5 not free" "5 not free" ...
##  $ 2014 Index of Economic Freedom: chr  "n/a" "3 moderately free" "4 mostly unfree" "5 repressed" ...
##  $ 2014 Press Freedom Index      : chr  "4 difficult situation" "3 noticeable problems" "4 difficult situation" "4 difficult situation" ...
##  $ 2012 Democracy Index          : chr  "5 authoritarian regime" "4 hybrid regime" "5 authoritarian regime" "5 authoritarian regime" ...
##  $ Rank                          : chr  "3" "99" "15" "64" ...
##  $ Score                         : chr  "10.062" "0.119" "5.831" "1.696" ...
##  $ hdi                           : num  0.37 0.75 0.71 0.51 0.81 0.73 0.94 0.9 0.73 0.8 ...
```



It is important to notice that the every dataset had different number of countries, I will not do the cleaning of that (but should be done). So, I am conscious that I will have a smaller set of countries. For now, let's play attention to what variables I have:

```r
str(dataCountries)
```

```
## 'data.frame':	147 obs. of  17 variables:
##  $ Country                       : chr  "Afghanistan" "Albania" "Algeria" "Angola" ...
##  $ Continent                     : chr  "Asia" "Europe" "Africa" "Africa" ...
##  $ Region                        : chr  "South Asia" "South East Europe" "Northern Africa" "Southern Africa" ...
##  $ Capital                       : chr  "Kabul" "Tirana" "Algiers" "Luanda" ...
##  $ FIPS                          : chr  "AF" "AL" "AG" "AO" ...
##  $ ISO (2)                       : chr  "AF" "AL" "DZ" "AO" ...
##  $ ISO (3)                       : chr  "AFG" "ALB" "DZA" "AGO" ...
##  $ ISO (No)                      : chr  "004" "008" "012" "024" ...
##  $ Internet                      : chr  "AF" "AL" "DZ" "AO" ...
##  $ Note                          : chr  "Â" "Â" "Â" "Â" ...
##  $ Freedom in the World 2014     : chr  "5 not free" "3 partly free" "5 not free" "5 not free" ...
##  $ 2014 Index of Economic Freedom: chr  "n/a" "3 moderately free" "4 mostly unfree" "5 repressed" ...
##  $ 2014 Press Freedom Index      : chr  "4 difficult situation" "3 noticeable problems" "4 difficult situation" "4 difficult situation" ...
##  $ 2012 Democracy Index          : chr  "5 authoritarian regime" "4 hybrid regime" "5 authoritarian regime" "5 authoritarian regime" ...
##  $ Rank                          : chr  "3" "99" "15" "64" ...
##  $ Score                         : chr  "10.062" "0.119" "5.831" "1.696" ...
##  $ hdi                           : num  0.37 0.75 0.71 0.51 0.81 0.73 0.94 0.9 0.73 0.8 ...
```

There are some variables I do not want, so I make them dissapear:

```r
dataCountries <- dataCountries[c(-4:-10, -13, -15)]  #I do not want these variable
```


Following, I alter the names of the variables:

```r
names(dataCountries) = c("Country", "Continent", "Region", "Freedom", "FreeMarket", "Democracy", "Terrorism", "HDI")
```


These is what I have so far:

```r
summary(dataCountries)
```

```
##    Country           Continent            Region            Freedom           FreeMarket         Democracy          Terrorism              HDI       
##  Length:147         Length:147         Length:147         Length:147         Length:147         Length:147         Length:147         Min.   :0.304  
##  Class :character   Class :character   Class :character   Class :character   Class :character   Class :character   Class :character   1st Qu.:0.524  
##  Mode  :character   Mode  :character   Mode  :character   Mode  :character   Mode  :character   Mode  :character   Mode  :character   Median :0.714  
##                                                                                                                                       Mean   :0.673  
##                                                                                                                                       3rd Qu.:0.810  
##                                                                                                                                       Max.   :0.955  
##                                                                                                                                       NA's   :1
```

```r
str(dataCountries)
```

```
## 'data.frame':	147 obs. of  8 variables:
##  $ Country   : chr  "Afghanistan" "Albania" "Algeria" "Angola" ...
##  $ Continent : chr  "Asia" "Europe" "Africa" "Africa" ...
##  $ Region    : chr  "South Asia" "South East Europe" "Northern Africa" "Southern Africa" ...
##  $ Freedom   : chr  "5 not free" "3 partly free" "5 not free" "5 not free" ...
##  $ FreeMarket: chr  "n/a" "3 moderately free" "4 mostly unfree" "5 repressed" ...
##  $ Democracy : chr  "5 authoritarian regime" "4 hybrid regime" "5 authoritarian regime" "5 authoritarian regime" ...
##  $ Terrorism : chr  "10.062" "0.119" "5.831" "1.696" ...
##  $ HDI       : num  0.37 0.75 0.71 0.51 0.81 0.73 0.94 0.9 0.73 0.8 ...
```

```r
head(dataCountries)
```

```
##       Country Continent            Region       Freedom        FreeMarket              Democracy Terrorism  HDI
## 1 Afghanistan      Asia        South Asia    5 not free               n/a 5 authoritarian regime    10.062 0.37
## 2     Albania    Europe South East Europe 3 partly free 3 moderately free        4 hybrid regime     0.119 0.75
## 3     Algeria    Africa   Northern Africa    5 not free   4 mostly unfree 5 authoritarian regime     5.831 0.71
## 4      Angola    Africa   Southern Africa    5 not free       5 repressed 5 authoritarian regime     1.696 0.51
## 5   Argentina  Americas     South America        1 free       5 repressed     2 flawed democracy     1.475 0.81
## 6     Armenia      Asia   South West Asia 3 partly free 3 moderately free        4 hybrid regime     0.188 0.73
```

We see nonstandard **n/a** values, and a mix of **number and category** which may bear trouble later, and also some columns should be read as factors and numbers but those are being read as characters, so we better change all of that.

First we get find the cells that use "n/a", get the index and then go back to the cell and eliminate that:

```r
for (i in 3:8) {
    indexes = c(grep("/", dataCountries[, i]))  # where is it?
    if (length(indexes) > 1) 
        dataCountries[indexes, i] = NA  # making the change  
}
```


In similar fashion we can find the cell whose that starts with a number and a space, and replace that combination by *nothing*:

```r
for (i in 1:7) {
    dataCountries[i] = sub("[0-9]+ ", "", dataCountries[, i])
}
```


Finally,we change the type of the columns:

```r
for (i in 2:7) {
    if (i > 6) {
        dataCountries[, i] = as.double(dataCountries[, i])
    } else {
        dataCountries[, i] = as.factor(dataCountries[, i])
    }
}
summary(dataCountries)
```

```
##    Country             Continent                Region          Freedom             FreeMarket                Democracy    Terrorism          HDI       
##  Length:147         Africa  :44   South West Asia  :18   free       :57   free           : 5   authoritarian regime:43   Min.   : 0.00   Min.   :0.304  
##  Class :character   Americas:24   Western Africa   :15   not free   :40   moderately free:46   flawed democracy    :48   1st Qu.: 0.02   1st Qu.:0.524  
##  Mode  :character   Asia    :42   South America    :11   partly free:50   mostly free    :23   full democracy      :22   Median : 1.13   Median :0.714  
##                     Europe  :34   Southern Africa  :10                    mostly unfree  :50   hybrid regime       :34   Mean   : 2.06   Mean   :0.673  
##                     Oceania : 3   South East Asia  : 8                    repressed      :17                             3rd Qu.: 3.58   3rd Qu.:0.810  
##                                   South East Europe: 8                    NA's           : 6                             Max.   :10.38   Max.   :0.955  
##                                   (Other)          :77                                                                                   NA's   :1
```


But, we will only wish to have those countries with all the indexes, which will finally save as a csv file:

```r
dataCountries = na.omit(dataCountries)
setwd("~/Documents/GITHUBrepositories/Tutorials/TemplatesR")
write.csv(dataCountries, "dataCountries.csv", row.names = FALSE)
```


You can access this data with the code:

```r
library(RCurl)
DATA_GithubCleaned <- getURL("https://raw.github.com/MAGALLANESJoseManuel/SocialScienceDataTools/master/TemplatesR/dataCountries.csv")
DATA_gitClean <- read.csv(text = DATA_GithubCleaned)
head(DATA_gitClean)
```

```
##     Country Continent            Region     Freedom      FreeMarket            Democracy Terrorism  HDI
## 1   Albania    Europe South East Europe partly free moderately free        hybrid regime     0.119 0.75
## 2   Algeria    Africa   Northern Africa    not free   mostly unfree authoritarian regime     5.831 0.71
## 3    Angola    Africa   Southern Africa    not free       repressed authoritarian regime     1.696 0.51
## 4 Argentina  Americas     South America        free       repressed     flawed democracy     1.475 0.81
## 5   Armenia      Asia   South West Asia partly free moderately free        hybrid regime     0.188 0.73
## 6 Australia   Oceania           Pacific        free            free       full democracy     1.720 0.94
```

```r
str(DATA_gitClean)
```

```
## 'data.frame':	141 obs. of  8 variables:
##  $ Country   : Factor w/ 141 levels "Albania","Algeria",..: 1 2 3 4 5 6 7 8 9 10 ...
##  $ Continent : Factor w/ 5 levels "Africa","Americas",..: 4 1 1 2 3 5 4 3 3 3 ...
##  $ Region    : Factor w/ 24 levels "Central Africa",..: 17 10 20 14 18 13 4 18 18 15 ...
##  $ Freedom   : Factor w/ 3 levels "free","not free",..: 3 2 2 1 3 1 1 2 2 3 ...
##  $ FreeMarket: Factor w/ 5 levels "free","moderately free",..: 2 4 5 5 2 1 3 2 3 4 ...
##  $ Democracy : Factor w/ 4 levels "authoritarian regime",..: 4 1 1 2 4 3 3 1 1 2 ...
##  $ Terrorism : num  0.119 5.831 1.696 1.475 0.188 ...
##  $ HDI       : num  0.75 0.71 0.51 0.81 0.73 0.94 0.9 0.73 0.8 0.52 ...
```

