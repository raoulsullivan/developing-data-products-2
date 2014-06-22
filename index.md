---
title       : Know your iris!
subtitle    : Developing Data Products assignment part 2
author      : Stephen Sullivan
job         : 
framework   : html5slides        # {io2012, html5slides, shower, dzslides, ...}
highlighter : prettify      # {highlight.js, prettify, highlight}
hitheme     : tomorrow      # 
widgets     : []            # {mathjax, quiz, bootstrap}
mode        : selfcontained # {standalone, draft}
knit        : slidify::knit2slides
--- .class #custbg
# Know your iris
<style>
#custbg {
  background-image:url('iris.jpg'); 
  background-repeat: no-repeat;
  background-position: center center;
  background-size: cover;
}
.tinytext {
    font-size:0.5em;
}
</style>
<p><i>A submission for the Coursera Developing Data Products assignment</i></p>
<p>Ever had an iris and been unable to classify it neatly?</p>
<p>Fear no more, the power of machine learning is here to help!</p>
<p>To access the iris classifier, <a href='https://raoulsullivan.shinyapps.io/assignment/'>click here</a></p>
<p>This presentation tells you more about how the iris classifier works.</p>

--- .class #id 

## The iris dataset





 
>'The Iris flower data set or Fisher's Iris data set is a multivariate data set introduced by Sir Ronald Fisher (1936) as an example of discriminant analysis.'

<p class='tinytext'>Wikipedia, <a href='http://en.wikipedia.org/wiki/Iris_flower_data_set'>http://en.wikipedia.org/wiki/Iris_flower_data_set</a>, accessed 22/6/2014</p>

It contains 150 observations of 5 variables: Sepal.Length, Sepal.Width, Petal.Length, Petal.Width, Species

<img src="figure/unnamed-chunk-2.png" title="plot of chunk unnamed-chunk-2" alt="plot of chunk unnamed-chunk-2" style="display: block; margin: auto;" />


---

## PreProcessing

Using the preProcess function from the Caret library, we can see that these 4 variables boil down to 2 principle components:


```r
preProc <- preProcess(iris[, -5], method = "pca", thresh = 0.9)
preProc
```

```
## 
## Call:
## preProcess.default(x = iris[, -5], method = "pca", thresh = 0.9)
## 
## Created from 150 samples and 4 variables
## Pre-processing: principal component signal extraction, scaled, centered 
## 
## PCA needed 2 components to capture 90 percent of the variance
```


---

## Model and prediction

It's quick to train a random forest model on a dataset of this size.


```r
intraining <- createDataPartition(iris$Species, p = 0.8, list = FALSE)
train <- iris[intraining, ]
preProc2 <- preProcess(train[, -5], method = "pca", thresh = 0.9)
trainingPCA <- predict(preProc2, train[, -5])
trainingPCA$Species <- train$Species
model <- train(Species ~ ., data = trainingPCA, method = "rf")
test <- iris[-intraining, ]
testingPCA <- predict(preProc2, test[, -5])
predict <- predict(model, testingPCA)
out <- table(predict == test$Species)
```


```
## 
## FALSE  TRUE 
##     4    26
```

---

## Output

So then we can run predictions against values submitted through the iris classification tool:

```r
input <- data.frame(Sepal.Length = 5.2, Sepal.Width = 2.8, Petal.Length = 1.8, 
    Petal.Width = 0.1)
inputPCA <- predict(preProc, input)
predict <- predict(model, inputPCA)
predict
```

```
## [1] setosa
## Levels: setosa versicolor virginica
```

<img src="figure/unnamed-chunk-7.png" title="plot of chunk unnamed-chunk-7" alt="plot of chunk unnamed-chunk-7" style="display: block; margin: auto;" />

