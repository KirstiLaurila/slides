---
title: "R TRAINING"
author: "Kirsti Laurila"
highlighter: highlight.js
output: pdf_document
job: '2015-06-16'
knit: slidify::knit2slides
mode: selfcontained
hitheme: tomorrow
framework: io2012
widgets: []
---

## With R you can make (almost) anything

* apps
   + [https://tuufaast.shinyapps.io/developing/](https://tuufaast.shinyapps.io/developing/)
* interactive graphs
   + [http://ouzor.github.io/blog/2014/11/21/interactive-visualizations.html](http://ouzor.github.io/blog/2014/11/21/interactive-visualizations.html)
* these slides...
   + R markdown language, you need knitr and slidify packages
   + [knitr](http://yihui.name/knitr/)
      - in CRAN
   + [slidify](http://ramnathv.github.io/slidify/)
      - install first devtools, after that install slidify from github 

---  

## Basics - data structures (1)

* Vectors
 * Atomic vectors: 
 ` character, logical, integer, double`
 * OBS! `is.numeric` does not tell if a vector is double, test with `is.double` if there is a danger of having integers
* Lists
* Factors
 * OBS! `as.numeric(as.character(factor))` !!
 * `stringsAsFactors = FALSE` when reading in data prevents factors to be created

---

## Basics - data structures (2) 

* Matrices (2-dim) and arrays
* Data frames
   * List!!! 
   * OBS! `data.frame()` turns character vectors to factors
     * `stringsAsFactors = FALSE`
   * ` plyr::rbind.fill()`
   * if one wants a to have lists, or matrices or arrays in data.frame columns,  use `I`

---

## Basics - functions

* Functions have three parts, `body()`, `formals()` and `environment()`
 * Except primitive functions ( written with C)
* Uses lazy evaluation
 * `force()` can be used 
* invisible return value with `invisible()`
* `on.exit()`
* in R everything is basically a function call
 * `'['(x, 3)`
 * `'+'(x, y)`
 * `'for'(i, 1:2, print(i))`

---

## Basics - reading in data (1)


```r
url <- "https://raw.githubusercontent.com/KirstiLaurila/R-training/master/csv.csv"
download.file(url, destfile="./data.csv")
data <- read.csv("./data.csv", header=FALSE)
```


```r
url1 <- "https://github.com/KirstiLaurila/R-training/raw/master/excel1.xlsx"
download.file(url1, destfile = "./data.xlsx", mode="wb")
library(xlsx)
data <- read.xlsx("./data.xlsx", sheetIndex=1, header=FALSE, rowIndex=1:4)
```

---

## Basics - reading in data (2)



```r
library(XML)
url2 <- "https://raw.githubusercontent.com/KirstiLaurila/R-training/master/Penguins.xml"
download.file(url2, destfile = "./data.xml")
dat_xml <- xmlInternalTreeParse("./data.xml")
rootNode <- xmlRoot(dat_xml)
xmlChildren(rootNode)
```


```r
library(data.table)
url <- "https://raw.githubusercontent.com/KirstiLaurila/R-training/master/csv.csv"
download.file(url, destfile = "./data.csv")
DT <- fread("./data.csv")
```


```r
read.delim("clipboard", header=FALSE)
```

---

## Useful functions (1)

* `cut` and `cut2` (Hmisc package)
  * `cut2` might give nicer looking cuts
* `split` 
* `subset`

```r
library(Hmisc)
values <- 1:20
cut(values, 10)
cut2(values, g=10)
```


```r
data(mtcars)
cutByMpg <- cut2(mtcars$mpg, g=5)
splits <- split(mtcars$wt, cutByMpg)
dfWithGroups <- data.frame(groups=I(splits), number=1:5)
```


```r
subset(mtcars,mtcars$am==1)
```

---

## Useful functions (2)

* `apply`
  * apply a function to a data.frame/array
  * `apply(mtcars,2,mean)`
* `lapply`
  * apply a function to a list
  * returns always a list
  * `lapply(mtcars,mean)`
* `eapply`
  * apply function for the items in env 


```r
env1=new.env()
env1$x <- 1:10
env1$y <- 21:10
eapply(env1,mean)
```

---
## Useful functions (3)

* `sapply`
  * simplified lapply, returns vector, or matrix, or array, or list
  * can be dangerous, vapply might be better (and faster)
  * `sapply(mtcars,mean)`
* `vapply`
  * like sapply but the output type is to be specified 
  * `vapply(mtcars,mean, numeric(1))`
  * `times=list(as.Date("2014-10-10"),Sys.time()); sapply(times,class); vapply(times,class, character(1))`

---

## Useful functions (4)

* `Map`
  * apply a function to a elements of vectors
  * `Map(rep,mtcars$mpg,ceiling(runif(length(mtcars[,1]))*10))`
* `mapply`
  * simplified Map  
  * `dataframe1=list(x=data.frame(c=runif(5),d=rpois(5,11)),`
    `             y=data.frame(c=rnorm(5),d=rbinom(5,10,0.6)))`
  * `mapply(function(x,y,z) x[y,z]*x[z,y], dataframe1,c(2,1),c(2,2))`
* `tapply`
  * apply function to defined groups
  * `tapply(mtcars$mpg, mtcars$am, mean)`
* `mclapply, mcMap`
  * package `multicore`, parallel `Map` and `lapply`
* `aggregate`
  * `aggregate(mpg~am+cyl, mean, data=mtcars)`

---

## Useful functions (5)

* `by` 
  * tapply for data frames
  * `by(mtcars[,c("mpg", "wt")], mtcars$am, function(x) lm(mpg~wt, data=x))`
* `merge`
  * joins data frames by columns or rows 

```r
persons <- data.frame(name=c("Anna", "Andrew", "Ken"), 
                      country=c("Finland", "Germany", "Germany"))
animals <- data.frame(animal=c("penguin", "giraffe", "elephant"), 
                      country=c("Finland", "Germany", "Spain"))
merge(persons,animals, all = TRUE)
```

---

## Useful functions (6)


* `Reduce`

```r
a=ceiling(10*runif(10)); b=ceiling(10*runif(10,1.5,3));c=ceiling(10*rnorm(10,1.5,3))
d=list(a,b,c)
Reduce(setdiff,d)
setdiff(setdiff(a,b),c)
```

---

## Exercises (1)

1. Take mtcars data set and add there a column with "automatic", when am=0 and "manual" otherwise. Then, use different apply-functions to compute mean for each numeric column. Do not use index or name of the column, but the solution should work for all data-frames regardlesss of their size and column names. 

2. Find at least two different ways to compute variance for the weight (mtcars) for each vs-am combination. 

3. let `x <- mtcars[,c("mpg", "wt", "hp")]` and `y <- ceiling(runif(32)*2)`. 
Now for each row i, compute for x a new column s so that
`s=log(x[i,y]) + x[i,c("hp")]` if hp>100, otherwise  `s=log(x[i,y])` use mapply/Map.

---

## Manipulating data with dplyr (1)

* works only with data frames (check `plyr` for different applys for other types)
* `tbl_df` is a wrapper for data frame
 * `mtcars2 <- tbl_df(mtcars)`
* `filter` - select specific data
 * `filter(mtcars2, am ==1, cyl>4)`
* `slice` - select rows
 * `slice(mtcars2,5:7)`
* `arrange` orders rows by columns
 * `arrange(mtcars2, mpg, wt)`
 * OBS! same as `mtcars2[order(mtcars2$mpg, mtcars2$wt),]` or `mtcars2[with(mtcars2,order(mpg,wt)),]`

---

## Manipulating data with dplyr (2)
 
* `select` select columns
 * `select(mtcars2,mpg,wt)`
 * `select (mtcars2, -mpg)`, `mtcars2[,-"mpg"]` does not work!
* `rename`
 * `rename(mtcars2, weight=wt)`
* `distinct`  
 * `distinct(mtcars2,cyl,vs)`
 * `distinct(select(mtcars2,cyl,vs)) == unique(mtcars2[,c("cyl", "vs")])`
* `mutate` create new columns also mutate_each
 * you can also refer to just created columns (unlike `transform` function)
 * `mutate(mtcars2, mpg_per_weight = mpg/wt, second = mpg_per_weight * carb )`
 * `mutate_each(mtcars2, funs(sum, .-2))`

---

## Manipulating data with dplyr (3)

* `transmute` like mutate but keep ony the new  variables 
 * `transmute(mtcars2, mpg_per_weight = mpg/wt, second = mpg_per_weight * carb )`
* `summarise` summarizes data frame also summarise_each
 * `summarise(mtcars2,mean(mpg))`
 * `summarise_each(mtcars2, funs(mean, sum(.*5)))`
* `sample_n` and `sample_frac` get random samples
  * `sample_n(mtcars2,5) sample_frac(mtcars2, 0.1)`
* `%>%` operator like piping in unix/linux
 * `mtcars2 %>% select(mpg,am,wt) %>% rename (weight= wt) %>% `
  `filter (weight>3) %>% arrange(weight)`

---

## Manipulating data with dplyr (4)

* window functions (provide several outputs)
 * can be used with `mutate` and `filter`
 * `mtcars2 %>% select(mpg,am,wt) %>% rename (weight= wt) %>%`
   `filter (weight>3) %>% arrange(weight) %>% mutate(rownumber= row_number())`
   `%>% mutate(wt2=cumsum(weight)) %>% filter(wt2 <20)`
 * see more in [vignette](http://cran.r-project.org/web/packages/dplyr/vignettes/window-functions.html)
 * connect to databases (postgres, mysql, sqlite)

```r
##OBS! does not work unless you have db mm on your localhost with work.module_models table
module_models <- tbl(src_postgres(dbname = "mm", host = "localhost",port = 5432, 
                user = "xsl",  password = NULL, 
                options="-c search_path=work"), "module_models")
result <- module_models %>% filter( output_id > 1 & key!= "value_mapping") 
          %>% arrange(output_id)
result$query 
```

---
## Exercises

load USArrets dataset and do the following with dplyr: 

1. Create data.frame that consists those states with more than 5 Murdests and 15 Rapes. 
2. Arrange the new data frame by 1. descending order by Assaults and 2. ascending by Rapes.
3. Select then other columns but UrbanPop
4. Create a new column to the data frame that has Murder column standardized, at the same command, compute the value for cumulative standard normal distribution in each of these standardized values. Add also rownumber for each line.
5. Do all this with piping


---

## Environments and lexical scoping (1)

* R uses lexical scoping 
 * The parameter values are searched for in the place where the function is created, not where it's called
 * what does the following script give for an answer: 

```r
a=3;b=4;
f<- function(x){
     x*b+a
}
g<- function(x){
     a=2; b=3;
     f(x)
}
g(2)
```

---

## Environments and lexical scoping (2)

* Variables (and functions) are looked for first inside the function, then in the environment where it's defined etc
* The variables are looked for when the function is called, not when it's defined

```r
f <- function() x
 x=10
 f()
 x=20
 f()
```


---

## Environments and lexical scoping (3)

* dynamic scoping
 * if one wants to use dynamic scoping (variables are looked in the env where the function is called, not where it's defined) one can set it's environment

```r
a=3;b=4;
f<- function(x){
   x*b+a
}
g<- function(x){
   environment(f)<- new.env()
   a=2; b=3;
   f(x)
}
g(2)
```
* be very careful with this, if you don't exactly know the dependencies of the function!!!
* `codetools::findGlobals(f)`

--- 
 
## Environments and lexical scoping (4)

* the working space's environment is `R_GlobalEnv`
* the environment of a function/ working space can be determined with `environment()`
* you can create environments with `new.env()`
* `emptyenv()`

```r
f<- function(x,y) x+y
environment(f)<- emptyenv()
f(1,2)
```
* `globalenv()`; `baseenv()`

---

## Environments and lexical scoping (5)


* you can set variables in an env either with assign or with `$`

```r
a <- 4; b<-6; env1 <- new.env(); env1$w<-1; assign("b", 3, env1)
get("b", env1); get("w", env1); 
get("a", env1)
```
* environments (except empty environment) have parent environment
 *`env1`:s parent environment is global environment where a is defined, it is searched next
 * `search()`
 * `attach/detach`
  * `attach(env1); search(); w; detach(); w`
* `<<-` operator assigns value in parent.env
 `i=1; f<-function(){i<-5; i<<-6; i}; f(); i`

---

## Exercises

1. Find the search path by using loop. Hint. you can test if environment is empty with
  `identical(env,emptyenv)`

2. Find the objects in the parent environment of your current environment an the objects in `baseenv()`

3. Create a function that prints variable xxx in global environment. Define also xxx <- 57. 
Check how function works. Create then a new environment and change the environment of the function to be the new environment. Assign a new value for xxxx in the new environment and see how the function behaves differently. 


---

## Intro to debugging, benchmarking and memory-optimization (1)

* `traceback()` shows the path to the error
* `debug()`, `browser()`, `debugSource()`
* `plyr::failwith()` is a nice alternative for `try()`
* `failwith(NA,f, quiet=TRUE)(2)`

---

## Intro to debugging, benchmarking and memory-optimization (2)

* `microbenchmarch`

```r
microbenchmark(
  "[10, 1]"      = mtcars[10, 1],
  "$mpg[10]"     = mtcars$mpg[10],
  "[[c(1, 10)]]" = mtcars[[c(1, 10)]],
  "[[1]][10]"    = mtcars[[1]][10],
  ".subset2"      = .subset2(mtcars, 1)[10]
)
```

---
## Intro to debugging, benchmarking and memory-optimization (3)

* `Rprof`


```r
testFunction <- function(){
  a <- c()
  for(i in 1:10){
    noisyWt <- rep(mtcars$wt+runif(length(mtcars$mpg)), 10000)
    model <- lm(rep(mtcars$mpg, 10000)~noisyWt)
    a <- cbind(a,predict(model,data.frame(noisyWt=rep(mtcars$wt,1000)) ))
  }
}
Rprof("profile1.out", line.profiling=TRUE)
testFunction()
Rprof(NULL)
summaryRprof("profile1.out")
```

---

## Intro to debugging, benchmarking and memory-optimization (4)


* vectorise!!!
* `pryr::mem_used()`, `mem_change()`
* 3.1.0 -> loops much faster as no deep copies anymore

---

## Exercises

1. Microbenchmark the solutions for the second exercise of the training. 

2. Check how the example for profiling changes if you vectorise the data.frame creation. 






---

## Object oriented programmin in R (1)

* Three systems
   * S3
     * elements of the class can be accessed with $
   * S4
     * elements of the class can be accessed with @
   * RC (reference classes)
      * more like 'traditional' oo-language objects
      * not included in the training
   * `pryr::otype()`

---

## Object oriented programmin in R (2)

* Methods for S3-classes 
    * UseMethod in the function generic
    * Greate then functions for (default and ) desired class(es)
* Methods for S4-classes 
    * Method name is first reserved with `setGeneric` (if the name is not yet in use)
    * `setMethod` defines the method

---

## S3 object example


```r
PenguinFan <- function(species="Emperor penguin", living="Antarctica"){
        object <- list(
                favouritespecies = species,
                continent = living
       )
        ## Set the name for the class
        class(object) <- append(class(object),"PenguinFan")
        return(object)
}
fan1 <- PenguinFan()
fan2 <- PenguinFan(species="Adelie")
ExtremePenguinFan <- function(species="Emperor penguin", living="Antarctica", 
                              visitsZoos = TRUE){
       object = PenguinFan(species,living)
       object$zoos = visitsZoos
       class(object) <- c("ExtremePenguinFan", "PenguinFan")
       return(object)
}
fan3 <- ExtremePenguinFan(living="Africa")
```

---

```r
setSpecies <- function(object, newValue){
                UseMethod("setSpecies",object)
              }

setSpecies.default <- function(object, newValue){
                print("For this class, species cannot be set.")
                return(object)
        }

setSpecies.PenguinFan <- function(object, newValue) {
      object$favouritespecies <- newValue
      return(object)
}
setSpecies(fan1,"New Penguin Species")
setSpecies(fan3, "Yet another species")

setSpecies.ExtremePenguinFan <- function(object, newValue) {
      object$favouritespecies <- paste("new species",newValue)
      return(object)
}
setSpecies(fan3, "Yet another species")
setSpecies(1:3)
```



---

## S4 Example (1)

```r
Penguin <- setClass(
        Class = "Penguin",
        # elements
        slots = c(
                height = "numeric",
                continent = "character"
                ),
        # optional default values
        prototype=list(
                height = 1.2,
                continent = "Antarctica"
                ),
        # Check validity of the object
        validity=function(object){
                if((object@height <= 0) ) {
                        return("Penguin height must be positive")
                }
                return(TRUE)
        }
        )
```

---


## S4 Example (2)


```r
penguin1 <- Penguin()
penguin2 <- Penguin(height=0.1, continent = "New Zealand")
penguin3 <- Penguin(height=0)
```

```r
AntarcticaPenguin <- setClass("AntarcticaPenguin",
  slots = c(likesIce = "logical"),
  contains = "Penguin")

AntarcticaPenguin(likesIce=TRUE)

penguin11 <- new("AntarcticaPenguin", likesIce=TRUE)
```

---

## S4 Example (3)

```r
setGeneric(name="setHeight",
          def=function(object,height){
            standardGeneric("setHeight")
          }
          )

setMethod(f="setHeight",
          signature="Penguin",
          definition=function(object,height){
              object@height <- height
              validObject(object)
              return(object)
          }
          )

penguin1_1 <- setHeight(penguin1,3)
penguin1_2 <- setHeight(penguin11,0.3)
```

---

## Extra exercise

1. Create your own R markdown document by the help of the [cheat sheet](http://www.rstudio.com/wp-content/uploads/2015/02/rmarkdown-cheatsheet.pdf)


---

## Extra reading

[http://blog.obeautifulcode.com/R/How-R-Searches-And-Finds-Stuff/](http://blog.obeautifulcode.com/R/How-R-Searches-And-Finds-Stuff/)

[http://www.cyclismo.org/tutorial/R/objectOriented.html](http://www.cyclismo.org/tutorial/R/objectOriented.html)

[cran.r-project.org/doc/contrib/Short-refcard.pdf](cran.r-project.org/doc/contrib/Short-refcard.pdf)



