R TUTORIAL (IV)
========================================================


I have not used R for the creation of the network form the bottom-up, but we may use it for plotting and exploration (as Python) and more importantly, for model building and model testing.  
Let's take a quick look at igraph. 

```r
library(igraph)
```


We first need to create the network using the two file we created in Python:

```r
el <- read.csv("WarsForR.csv", header = T)  #list of edges
```

```
## Warning: cannot open file 'WarsForR.csv': No such file or directory
```

```
## Error: cannot open the connection
```

```r
at <- read.csv("WarsForR_attr.csv", header = T, as.is = T)  #node attributes
```

```
## Warning: cannot open file 'WarsForR_attr.csv': No such file or directory
```

```
## Error: cannot open the connection
```

```r
warsGraphIgraph2 = graph.data.frame(el, directed = FALSE, vertices = at)
```

```
## Error: cannot coerce class ""function"" to a data.frame
```

```r
x = V(warsGraphIgraph2)$longitude
```

```
## Error: object 'warsGraphIgraph2' not found
```

```r
y = V(warsGraphIgraph2)$latitude
```

```
## Error: object 'warsGraphIgraph2' not found
```

```r
l2 = cbind(x, y)
```

```
## Error: object 'x' not found
```

```r
plot.igraph(warsGraphIgraph2, layout = l2, edge.width = E(warsGraphIgraph2)$duration)  #width of edge based on duration!
```

```
## Error: object 'warsGraphIgraph2' not found
```

Once we have the network in igraph we can continue using all its tools. Particularly, I recommend reviewing the documentation found at [igraph wiki](http://igraph.sourceforge.net/igraphbook/.

However, R will become extremely important to do network modelling, particularly **ERGM** or **TERGM** models using the **statnet**. 


```r
library(statnet)
```


The documentation of statnet is available in  its [website](http://statnet.org/). However, here we will give you the first and most important step!... read a network:

```r
el <- read.csv("WarsForR.csv", header = T)  #list of edges
```

```
## Warning: cannot open file 'WarsForR.csv': No such file or directory
```

```
## Error: cannot open the connection
```

```r
at <- read.csv("WarsForR_attr.csv", header = T, as.is = T)  #node attributes
```

```
## Warning: cannot open file 'WarsForR_attr.csv': No such file or directory
```

```
## Error: cannot open the connection
```

As we know, the attributes of the nodes consists of only its coordinates. But to vuild a model it is better that we have other kind of attributes, so let's get back our terrorism score, and merge it with the attribute data:

```r
RegionCode = read.csv("dataCountries.csv")[, c(1, 3)]
names(RegionCode)
```

```
## [1] "Country" "Region"
```

```r
RegionCode$Region = as.numeric(RegionCode$Region)
str(RegionCode$Region)
```

```
##  num [1:141] 17 10 20 14 18 13 4 18 18 15 ...
```

```r
at = merge(at, RegionCode, by.x = "country", by.y = "Country", all.x = T)
```

```
## Error: object 'at' not found
```


For sure there will be some some countries without this score (i.e. Soviet Union), but let's keep it simple now. Anyway, before using this merge, let's check what we have and get rid of what we do not need:

```r
names(at)  #variables after the merge
```

```
## Error: object 'at' not found
```


Now we can create our network in statnet:

```r
nw<-network(el, #edgelist
            matrix.typ="edgelist",          #format of 'el'
            vertex.attr=at,                 #where the node attributes are
            vertex.attrnames=colnames(at),  #names of node attributes
            ignore.eval=FALSE,
            names.eval=c("fatalities","duration","name"),#names of edge attributes
            directed=F, hyper=F, loops=F, multiple=F, bipartite=F)
```

```
## Error: argument is of length zero
```


And can see it our statnet network here (two versions):

```r
gplot(nw)
```

```
## Error: object 'nw' not found
```

```r
gplot(nw, displaylabels = TRUE, usearrows = F)
```

```
## Error: object 'nw' not found
```

```r
lnw = cbind(nw %v% "longitude", nw %v% "latitude")
```

```
## Error: object 'nw' not found
```

...and of course:

```r
gplot(nw, displaylabels = TRUE, coord = lnw, usearrows = F, edge.lwd = nw %e% "duration"/2)
```

```
## Error: object 'nw' not found
```


Finally, we can show you how you can model a network in statnet:

```r
whyWars <- ergm(nw ~ edges + nodematch("Region"))
```

```
## Error: The network in the formula 'nw ~ edges + nodematch("Region")' cannot be found.
```

This model wants to know, for instance, how the terrorism index increases the probability of a link formation. We can see the result below:

```r
summary(whyWars)
```

```
## Error: object 'whyWars' not found
```


```r
simWhyWars <- simulate(whyWars)
```

```
## Error: object 'whyWars' not found
```

```r
plot(simWhyWars)
```

```
## Error: object 'simWhyWars' not found
```

There are many more steps involved in network modelling, and a particularly helpful tutorial on ergm can also be found [here](http://statnet.csde.washington.edu/NME2013/day2/ergm%20tutorial.html).
