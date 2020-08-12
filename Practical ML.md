# Course-Project
Plain text
End a line with two spaces to start a new paragraph.
*italics* and _italics_
**bold** and __bold__
superscript^2^
~~strikethrough~~
[link](www.rstudio.com) 
```{r}
##Data Availibity
#Training Data: https://d396qusza40orc.cloudfront.net/predmachlearn/pml-training.csv
#Test data : https://d396qusza40orc.cloudfront.net/predmachlearn/pml-testing.csv
#The Training Data will be used to develop algorithm and Test Data to make our final predictions..

##Required Packages
#Installing and Loading Packages.
install.packages('caret')
install.packages('lattice')
install.packages('randomForest')
install.packages('rpart')
install.packages('rpart.plot')
install.packages('RColorBrewer')
install.packages('rattle')
install.packages('ggplot2')
install.packages('e1071')
library(e1071)
library(caret)
library(randomForest)
library(rpart)
library(rpart.plot)
library(RColorBrewer)
library(rattle)
library(ggplot2)

#Getting Working Directory
getwd()

#Loading Training and Test Datasets
train = read.csv('pml-training.csv',na.strings=c('','NA'))
test = read.csv('pml-testing.csv',na.strings=c('','NA'))

#exploring data
head(train)
tail(train)

##Data Set Partition
#First, we split the training data set into two subsamples, the 70% will be used as training set and remaining 30% will be used as testing set.

inTrain <- createDataPartition(train$classe, p = 0.7, list = FALSE)
train1 <- train[inTrain, ]
train2 <- train[-inTrain, ]

##Performing Data Cleaning.
#removing near-zero variance predictors
nZV <- nearZeroVar(train)
train1 <- train1[, -nZV]
train2 <- train2[, -nZV]
#removing predictors with NA values
train1 <- train1[, colSums(is.na(train1)) == 0]
train2 <- train2[, colSums(is.na(train2)) == 0]
#removing 6 columns unfit for prediction 
train1 <- train1[, -(1:5)]
train2 <- train[, -(1:5)]

#Model Building.
#We chose to fit a random forest model. 
#The cross-validation is set to draw a subset of the data three different times.

model1 <- train(classe ~., method = "rf", data = train1, verbose = TRUE, trControl = trainControl(method="cv"), number = 3)

#prediction using model1 on training set 1and training set 2
prediction1 <- predict(model1, train1)
prediction2 <- predict(model1, train2)

#Confusion Matrix
confusionMatrix(prediction1, as.factor(train1$classe))
confusionMatrix(prediction2, as.factor(train2$classe))
#we get almost 99% accuracy that is a very high accuracy so we didnt actually overfit the model and also we can say that out of sample error is about 1%.

##Testing Model
#were testing our model using testing set.
test <- test[, colSums(is.na(test)) == 0]
test <- test[, -(1:5)]
nzvt <- nearZeroVar(test)
test <- test[, -nzvt]
#Prediction using model1 on testing dataset
prediction <- predict(model1, test)
prediction
```
