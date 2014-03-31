R TUTORIAL (I)
========================================================


This is our first "basic" step-by-step tutorial where I will simply show how to collect some data,

In general, social data appear in a spreadsheet-like format, where different columns can have different **types** of values, that is *numeric* and *string*, while numeric can also represent different *scales*.However, data collected from social events may not follow a strict protocol and may bring some challenges when we need to work on it.For instance, take a look at the image below:


<img src="http://i.imgur.com/ioOhKrL.jpg" height="500" width="800" align=center> 


 * Source:<a href='http://en.wikipedia.org/wiki/List_of_border_wars' target='_blank'>WIKIPEDIA</a>

Anyway, if the source is idenytified and it looks like the image above we have several options using R. First we can think of copying and pasting the data into an **MSExcel** file, where we can save it as **csv**. If the data is in that format, we can get it quickly with a simple command:


```r
setwd("~/Documents/GITHUBrepositories/Tutorials/TemplatesR")
DATA_LocalFile = read.csv("warI.csv")
head(DATA_LocalFile)
```

```
##   Start                      Finish                   Conflict                              Combatants Fatalities
## 1  1947                        1948 Indo-Pakistani War of 1947                    Pakistan \xd0  India     ~3,000
## 2  1950 Present (CF signed in 1953)                 Korean War              South Korea -  North Korea 2 419 010+
## 3  1962                        1962            Sino-Indian War                         PRC \xd0  India     ~4,000
## 4  1965                        1965 Indo-Pakistani War of 1965                    Pakistan \xd0  India     ~6,800
## 5  1966                        1989   South African Border War               Angola \xd0  South Africa    Unknown
## 6  1967                        1967             Chola incident  India \xd0  People's Republic of China         ~5
```


It is sometimes a good idea to store it in **GoogleDrive** so that you do not depend on your hard drive availability, as shown below:

<img src="http://i.imgur.com/d8gVtyH.jpg" height="500" width="800" align=center> 

You can see the previous steps in <a href="http://blog.revolutionanalytics.com/2009/09/how-to-use-a-google-spreadsheet-as-data-in-r.html" target="_blank">http://blog.revolutionanalytics.com/</a>.  
In our case, the proces is:

```r
require(RCurl)
```

```
## Loading required package: RCurl
## Loading required package: bitops
```

```r
CsvGDocs <- getURL("https://docs.google.com/spreadsheet/pub?key=0AhVqDdZgThPldEZpWlc2Z3FuVm8taTlBVlc1a3VnT2c&output=csv")
DATA_Gdocs = read.csv(textConnection(CsvGDocs))
head(DATA_Gdocs)
```

```
##   Start                      Finish                   Conflict                                Combatants Fatalities
## 1  1947                        1948 Indo-Pakistani War of 1947                   Pakistan Ã\u0090  India     ~3,000
## 2  1950 Present (CF signed in 1953)                 Korean War                South Korea -  North Korea 2 419 010+
## 3  1962                        1962            Sino-Indian War                        PRC Ã\u0090  India     ~4,000
## 4  1965                        1965 Indo-Pakistani War of 1965                   Pakistan Ã\u0090  India     ~6,800
## 5  1966                        1989   South African Border War              Angola Ã\u0090  South Africa    Unknown
## 6  1967                        1967             Chola incident India Ã\u0090  People's Republic of China         ~5
```


There are other alternatives as **dropbox**, but a much better one could be  **GitHub**, where not only your data can be stored, but specially your code:

```r
library(RCurl)
DATA_Github <- getURL("https://raw.github.com/MAGALLANESJoseManuel/SocialScienceDataTools/master/TemplatesR/warI.csv")
DATA_git <- read.csv(text = DATA_Github)
head(DATA_git)
```

```
##   Start                      Finish                   Conflict                           Combatants Fatalities
## 1  1947                        1948 Indo-Pakistani War of 1947                    Pakistan Ð  India     ~3,000
## 2  1950 Present (CF signed in 1953)                 Korean War           South Korea -  North Korea 2 419 010+
## 3  1962                        1962            Sino-Indian War                         PRC Ð  India     ~4,000
## 4  1965                        1965 Indo-Pakistani War of 1965                    Pakistan Ð  India     ~6,800
## 5  1966                        1989   South African Border War               Angola Ð  South Africa    Unknown
## 6  1967                        1967             Chola incident  India Ð  People's Republic of China         ~5
```

We can also get the table from where it has been published (Wikipedia):

```r
Link = "http://en.wikipedia.org/wiki/List_of_border_wars"
whichTable = 3
library(XML)
wikiDataWWW = getNodeSet(htmlParse(Link), "//table")[[whichTable]]
DATA_www = readHTMLTable(wikiDataWWW)
head(DATA_www)
```

```
##      V1                          V2                         V3                                 V4         V5
## 1 Start                      Finish                   Conflict                         Combatants Fatalities
## 2  1947                        1948 Indo-Pakistani War of 1947      Pakistan â\u0080\u0093  India     ~3,000
## 3  1950 Present (CF signed in 1953)                 Korean War         South Korea -  North Korea 2 419 010+
## 4  1962                        1962            Sino-Indian War           PRC â\u0080\u0093  India     ~4,000
## 5  1965                        1965 Indo-Pakistani War of 1965      Pakistan â\u0080\u0093  India     ~6,800
## 6  1966                        1989   South African Border War Angola â\u0080\u0093  South Africa    Unknown
```


Evidently, some more work may be needed, we will see that later.
