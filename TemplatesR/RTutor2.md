R TUTORIAL (II)
========================================================


In a previous post we had a table from wikipedia with information of interest. In this code, I will show some variations in data **collection** and **cleaning**.  

Following from the previous tutorial we had a table from the link:

```r
Link = "http://en.wikipedia.org/wiki/List_of_border_wars"
```


As it can be seen below, this wikipage has many tables:

<img src="http://i.imgur.com/hOa1tml.jpg" height="500" width="800" align=center> 

First, let's get the table as in the previous post:


```r
library(XML)
whichTable = 3
dirtyTable = getNodeSet(htmlParse(Link), "//table")[[whichTable]]
```


Then, let's get the **table** already known, and see its first rows(using *head*):

```
##       V1                          V2                                           V3                                              V4             V5
## 1  Start                      Finish                                     Conflict                                      Combatants     Fatalities
## 2   1947                        1948                   Indo-Pakistani War of 1947                   Pakistan â\u0080\u0093  India         ~3,000
## 3   1950 Present (CF signed in 1953)                                   Korean War                      South Korea -  North Korea     2 419 010+
## 4   1962                        1962                              Sino-Indian War                        PRC â\u0080\u0093  India         ~4,000
## 5   1965                        1965                   Indo-Pakistani War of 1965                   Pakistan â\u0080\u0093  India         ~6,800
## 6   1966                        1989                     South African Border War              Angola â\u0080\u0093  South Africa        Unknown
## 7   1967                        1967                               Chola incident India â\u0080\u0093  People's Republic of China             ~5
## 8   1969                        1969                  Sino-Soviet border conflict                 PRC â\u0080\u0093  Soviet Union        Unknown
## 9   1971                        1971                   Indo-Pakistani War of 1971                   Pakistan â\u0080\u0093  India        ~4,000+
## 10  1979                        1990          Sino-Vietnamese conflicts 1979-1990                    Vietnam â\u0080\u0093  China        Unclear
## 11  1980                        1988                    Iranâ\u0080\u0093Iraq War                        Iran â\u0080\u0093  Iraq 1-2 million[1]
## 12  1981                        1981                                 Paquisha War                     Ecuador â\u0080\u0093  Peru            ~10
## 13  1982                        1982 1982 Ethiopianâ\u0080\u0093Somali Border War                 Ethiopia â\u0080\u0093  Somalia        Unknown
## 14  1984                        1987                             Siachen Conflict                   Pakistan â\u0080\u0093  India        ~2,400.
## 15  1985                        1985                            Agacher Strip War                Burkina Faso â\u0080\u0093  Mali            179
## 16  1987                        1987                    1987 Sino-Indian skirmish India â\u0080\u0093  People's Republic of China        Unknown
## 17  1987                        1988          Thaiâ\u0080\u0093Laotian Border War                    Thailand â\u0080\u0093  Laos         ~1,000
## 18  1989                        1991    Mauritaniaâ\u0080\u0093Senegal Border War               Mauritania â\u0080\u0093  Senegal        Unknown
## 19  1995                        1995                                   Cenepa War                     Ecuador â\u0080\u0093  Peru           ~450
## 20  1998                        2000                       Eritrean-Ethiopian War                 Ethiopia â\u0080\u0093  Eritrea        ~70,000
## 21  1999                        1999                                   Kargil War                   India â\u0080\u0093  Pakistan         ~4,500
```


The code so far is not recognising the first row as the names of the columns, so we solve that setting header as *True*:


```r
cleanTable = readHTMLTable(dirtyTable, header = T)
cleanTable
```

```
##    Start                      Finish                                     Conflict                                      Combatants     Fatalities
## 1   1947                        1948                   Indo-Pakistani War of 1947                   Pakistan â\u0080\u0093  India         ~3,000
## 2   1950 Present (CF signed in 1953)                                   Korean War                      South Korea -  North Korea     2 419 010+
## 3   1962                        1962                              Sino-Indian War                        PRC â\u0080\u0093  India         ~4,000
## 4   1965                        1965                   Indo-Pakistani War of 1965                   Pakistan â\u0080\u0093  India         ~6,800
## 5   1966                        1989                     South African Border War              Angola â\u0080\u0093  South Africa        Unknown
## 6   1967                        1967                               Chola incident India â\u0080\u0093  People's Republic of China             ~5
## 7   1969                        1969                  Sino-Soviet border conflict                 PRC â\u0080\u0093  Soviet Union        Unknown
## 8   1971                        1971                   Indo-Pakistani War of 1971                   Pakistan â\u0080\u0093  India        ~4,000+
## 9   1979                        1990          Sino-Vietnamese conflicts 1979-1990                    Vietnam â\u0080\u0093  China        Unclear
## 10  1980                        1988                    Iranâ\u0080\u0093Iraq War                        Iran â\u0080\u0093  Iraq 1-2 million[1]
## 11  1981                        1981                                 Paquisha War                     Ecuador â\u0080\u0093  Peru            ~10
## 12  1982                        1982 1982 Ethiopianâ\u0080\u0093Somali Border War                 Ethiopia â\u0080\u0093  Somalia        Unknown
## 13  1984                        1987                             Siachen Conflict                   Pakistan â\u0080\u0093  India        ~2,400.
## 14  1985                        1985                            Agacher Strip War                Burkina Faso â\u0080\u0093  Mali            179
## 15  1987                        1987                    1987 Sino-Indian skirmish India â\u0080\u0093  People's Republic of China        Unknown
## 16  1987                        1988          Thaiâ\u0080\u0093Laotian Border War                    Thailand â\u0080\u0093  Laos         ~1,000
## 17  1989                        1991    Mauritaniaâ\u0080\u0093Senegal Border War               Mauritania â\u0080\u0093  Senegal        Unknown
## 18  1995                        1995                                   Cenepa War                     Ecuador â\u0080\u0093  Peru           ~450
## 19  1998                        2000                       Eritrean-Ethiopian War                 Ethiopia â\u0080\u0093  Eritrea        ~70,000
## 20  1999                        1999                                   Kargil War                   India â\u0080\u0093  Pakistan         ~4,500
```


Now we are concern with the strange characters in the fourth column, we will try to see if indicating UTF-8 as encoding can help solve this situation:

```r
dirtyTable = getNodeSet(htmlParse(Link, encoding = "UTF-8"), "//table")[[whichTable]]
cleanTable = readHTMLTable(dirtyTable, header = T)
cleanTable
```

```
##    Start                      Finish                            Conflict                          Combatants     Fatalities
## 1   1947                        1948          Indo-Pakistani War of 1947                   Pakistan –  India         ~3,000
## 2   1950 Present (CF signed in 1953)                          Korean War          South Korea -  North Korea     2 419 010+
## 3   1962                        1962                     Sino-Indian War                        PRC –  India         ~4,000
## 4   1965                        1965          Indo-Pakistani War of 1965                   Pakistan –  India         ~6,800
## 5   1966                        1989            South African Border War              Angola –  South Africa        Unknown
## 6   1967                        1967                      Chola incident India –  People's Republic of China             ~5
## 7   1969                        1969         Sino-Soviet border conflict                 PRC –  Soviet Union        Unknown
## 8   1971                        1971          Indo-Pakistani War of 1971                   Pakistan –  India        ~4,000+
## 9   1979                        1990 Sino-Vietnamese conflicts 1979-1990                    Vietnam –  China        Unclear
## 10  1980                        1988                       Iran–Iraq War                        Iran –  Iraq 1-2 million[1]
## 11  1981                        1981                        Paquisha War                     Ecuador –  Peru            ~10
## 12  1982                        1982    1982 Ethiopian–Somali Border War                 Ethiopia –  Somalia        Unknown
## 13  1984                        1987                    Siachen Conflict                   Pakistan –  India        ~2,400.
## 14  1985                        1985                   Agacher Strip War                Burkina Faso –  Mali            179
## 15  1987                        1987           1987 Sino-Indian skirmish India –  People's Republic of China        Unknown
## 16  1987                        1988             Thai–Laotian Border War                    Thailand –  Laos         ~1,000
## 17  1989                        1991       Mauritania–Senegal Border War               Mauritania –  Senegal        Unknown
## 18  1995                        1995                          Cenepa War                     Ecuador –  Peru           ~450
## 19  1998                        2000              Eritrean-Ethiopian War                 Ethiopia –  Eritrea        ~70,000
## 20  1999                        1999                          Kargil War                   India –  Pakistan         ~4,500
```


This is so much better. Now that we have got rid of the most obvious issues, let's see if we other things are hidding somewhere.Let's simply get a *summary* and the *structure* of this table:

```r
summary(cleanTable)
str(cleanTable)
```

```
##      Start        Finish                               Conflict                                Combatants   Fatalities
##  1987   : 2   1987   : 2   1982 Ethiopian–Somali Border War: 1   Pakistan –  India                  :4    Unknown: 5  
##  1947   : 1   1988   : 2   1987 Sino-Indian skirmish       : 1   Ecuador –  Peru                    :2    ~1,000 : 1  
##  1950   : 1   1948   : 1   Agacher Strip War               : 1   India –  People's Republic of China:2    ~10    : 1  
##  1962   : 1   1962   : 1   Cenepa War                      : 1   Angola –  South Africa             :1    ~2,400.: 1  
##  1965   : 1   1965   : 1   Chola incident                  : 1   Burkina Faso –  Mali               :1    ~3,000 : 1  
##  1966   : 1   1967   : 1   Eritrean-Ethiopian War          : 1   Ethiopia –  Eritrea                :1    ~4,000 : 1  
##  (Other):13   (Other):12   (Other)                         :14   (Other)                            :9    (Other):10
```

```
## 'data.frame':	20 obs. of  5 variables:
##  $ Start     : Factor w/ 19 levels "1947","1950",..: 1 2 3 4 5 6 7 8 9 10 ...
##  $ Finish    : Factor w/ 18 levels "1948","1962",..: 1 18 2 3 12 4 5 6 13 11 ...
##  $ Conflict  : Factor w/ 20 levels "1982 Ethiopian–Somali Border War",..: 7 12 16 8 19 5 17 9 18 10 ...
##  $ Combatants: Factor w/ 15 levels "Angola –  South Africa",..: 10 13 11 10 1 7 12 10 15 8 ...
##  $ Fatalities: Factor w/ 16 levels "~1,000","~10",..: 4 14 5 10 16 9 16 6 15 12 ...
```


The last column has obvious problems, and we can try to replace the wrong symbols. However, from the last result we see it is a factor instead of a string, we need to correct that (if it is not a string, we can not operate on them with string functions).


```r
dirtyTable = getNodeSet(htmlParse(Link, encoding = "UTF-8"), "//table")[[whichTable]]
cleanTable = readHTMLTable(dirtyTable, header = T, trim = T, stringsAsFactors = F)
indexmillion = grep("million", cleanTable$Fatalities)
cleanTable$Fatalities[indexmillion] = "2000000"
indexunknown = grep("Un", cleanTable$Fatalities)
cleanTable$Fatalities[c(indexunknown)] = NA
cleanTable
```

```
##    Start                      Finish                            Conflict                          Combatants Fatalities
## 1   1947                        1948          Indo-Pakistani War of 1947                   Pakistan –  India     ~3,000
## 2   1950 Present (CF signed in 1953)                          Korean War          South Korea -  North Korea 2 419 010+
## 3   1962                        1962                     Sino-Indian War                        PRC –  India     ~4,000
## 4   1965                        1965          Indo-Pakistani War of 1965                   Pakistan –  India     ~6,800
## 5   1966                        1989            South African Border War              Angola –  South Africa       <NA>
## 6   1967                        1967                      Chola incident India –  People's Republic of China         ~5
## 7   1969                        1969         Sino-Soviet border conflict                 PRC –  Soviet Union       <NA>
## 8   1971                        1971          Indo-Pakistani War of 1971                   Pakistan –  India    ~4,000+
## 9   1979                        1990 Sino-Vietnamese conflicts 1979-1990                    Vietnam –  China       <NA>
## 10  1980                        1988                       Iran–Iraq War                        Iran –  Iraq    2000000
## 11  1981                        1981                        Paquisha War                     Ecuador –  Peru        ~10
## 12  1982                        1982    1982 Ethiopian–Somali Border War                 Ethiopia –  Somalia       <NA>
## 13  1984                        1987                    Siachen Conflict                   Pakistan –  India    ~2,400.
## 14  1985                        1985                   Agacher Strip War                Burkina Faso –  Mali        179
## 15  1987                        1987           1987 Sino-Indian skirmish India –  People's Republic of China       <NA>
## 16  1987                        1988             Thai–Laotian Border War                    Thailand –  Laos     ~1,000
## 17  1989                        1991       Mauritania–Senegal Border War               Mauritania –  Senegal       <NA>
## 18  1995                        1995                          Cenepa War                     Ecuador –  Peru       ~450
## 19  1998                        2000              Eritrean-Ethiopian War                 Ethiopia –  Eritrea    ~70,000
## 20  1999                        1999                          Kargil War                   India –  Pakistan     ~4,500
```


We see a better table. Now we need to get rid of those problematic symbols and empty spaces.We will extra columns to show the conversion process.

```r
cleanTable$Fatalities2 = gsub("[[:punct:]]", "", cleanTable$Fatalities)
cleanTable$Fatalities3 = gsub(" ", "", cleanTable$Fatalities2)
cleanTable
```

```
##    Start                      Finish                            Conflict                          Combatants Fatalities Fatalities2 Fatalities3
## 1   1947                        1948          Indo-Pakistani War of 1947                   Pakistan –  India     ~3,000        3000        3000
## 2   1950 Present (CF signed in 1953)                          Korean War          South Korea -  North Korea 2 419 010+   2 419 010     2419010
## 3   1962                        1962                     Sino-Indian War                        PRC –  India     ~4,000        4000        4000
## 4   1965                        1965          Indo-Pakistani War of 1965                   Pakistan –  India     ~6,800        6800        6800
## 5   1966                        1989            South African Border War              Angola –  South Africa       <NA>        <NA>        <NA>
## 6   1967                        1967                      Chola incident India –  People's Republic of China         ~5           5           5
## 7   1969                        1969         Sino-Soviet border conflict                 PRC –  Soviet Union       <NA>        <NA>        <NA>
## 8   1971                        1971          Indo-Pakistani War of 1971                   Pakistan –  India    ~4,000+        4000        4000
## 9   1979                        1990 Sino-Vietnamese conflicts 1979-1990                    Vietnam –  China       <NA>        <NA>        <NA>
## 10  1980                        1988                       Iran–Iraq War                        Iran –  Iraq    2000000     2000000     2000000
## 11  1981                        1981                        Paquisha War                     Ecuador –  Peru        ~10          10          10
## 12  1982                        1982    1982 Ethiopian–Somali Border War                 Ethiopia –  Somalia       <NA>        <NA>        <NA>
## 13  1984                        1987                    Siachen Conflict                   Pakistan –  India    ~2,400.        2400        2400
## 14  1985                        1985                   Agacher Strip War                Burkina Faso –  Mali        179         179         179
## 15  1987                        1987           1987 Sino-Indian skirmish India –  People's Republic of China       <NA>        <NA>        <NA>
## 16  1987                        1988             Thai–Laotian Border War                    Thailand –  Laos     ~1,000        1000        1000
## 17  1989                        1991       Mauritania–Senegal Border War               Mauritania –  Senegal       <NA>        <NA>        <NA>
## 18  1995                        1995                          Cenepa War                     Ecuador –  Peru       ~450         450         450
## 19  1998                        2000              Eritrean-Ethiopian War                 Ethiopia –  Eritrea    ~70,000       70000       70000
## 20  1999                        1999                          Kargil War                   India –  Pakistan     ~4,500        4500        4500
```



```r
cleanTable$Fatalities = as.numeric(cleanTable$Fatalities3)
cleanTable$Fatalities2 = NULL
cleanTable$Fatalities3 = NULL
cleanTable
```

```
##    Start                      Finish                            Conflict                          Combatants Fatalities
## 1   1947                        1948          Indo-Pakistani War of 1947                   Pakistan –  India       3000
## 2   1950 Present (CF signed in 1953)                          Korean War          South Korea -  North Korea    2419010
## 3   1962                        1962                     Sino-Indian War                        PRC –  India       4000
## 4   1965                        1965          Indo-Pakistani War of 1965                   Pakistan –  India       6800
## 5   1966                        1989            South African Border War              Angola –  South Africa         NA
## 6   1967                        1967                      Chola incident India –  People's Republic of China          5
## 7   1969                        1969         Sino-Soviet border conflict                 PRC –  Soviet Union         NA
## 8   1971                        1971          Indo-Pakistani War of 1971                   Pakistan –  India       4000
## 9   1979                        1990 Sino-Vietnamese conflicts 1979-1990                    Vietnam –  China         NA
## 10  1980                        1988                       Iran–Iraq War                        Iran –  Iraq    2000000
## 11  1981                        1981                        Paquisha War                     Ecuador –  Peru         10
## 12  1982                        1982    1982 Ethiopian–Somali Border War                 Ethiopia –  Somalia         NA
## 13  1984                        1987                    Siachen Conflict                   Pakistan –  India       2400
## 14  1985                        1985                   Agacher Strip War                Burkina Faso –  Mali        179
## 15  1987                        1987           1987 Sino-Indian skirmish India –  People's Republic of China         NA
## 16  1987                        1988             Thai–Laotian Border War                    Thailand –  Laos       1000
## 17  1989                        1991       Mauritania–Senegal Border War               Mauritania –  Senegal         NA
## 18  1995                        1995                          Cenepa War                     Ecuador –  Peru        450
## 19  1998                        2000              Eritrean-Ethiopian War                 Ethiopia –  Eritrea      70000
## 20  1999                        1999                          Kargil War                   India –  Pakistan       4500
```


The final step would the to clean the cell where a problematic text is input for *finish* year:

```r
indexyear = grep("CF", cleanTable$Finish)
cleanTable$Finish[indexyear] = "1953"
```


We can now save the file to our github repository (once we push it from Rstudio):

```r
setwd("~/Documents/GITHUBrepositories/Tutorials/TemplatesR")
write.csv(cleanTable, "wikitable.csv")
```


You can work with this file now:

```r
library(RCurl)
```

```
## Loading required package: bitops
```

```r
DATA_GithubCleaned <- getURL("https://raw.github.com/MAGALLANESJoseManuel/SocialScienceDataTools/master/TemplatesR/wikitable.csv")
DATA_gitClean <- read.csv(text = DATA_GithubCleaned)
head(DATA_gitClean)
```

```
##   X Start Finish                   Conflict                          Combatants Fatalities
## 1 1  1947   1948 Indo-Pakistani War of 1947                   Pakistan –  India       3000
## 2 2  1950   1953                 Korean War          South Korea -  North Korea    2419010
## 3 3  1962   1962            Sino-Indian War                        PRC –  India       4000
## 4 4  1965   1965 Indo-Pakistani War of 1965                   Pakistan –  India       6800
## 5 5  1966   1989   South African Border War              Angola –  South Africa         NA
## 6 6  1967   1967             Chola incident India –  People's Republic of China          5
```

