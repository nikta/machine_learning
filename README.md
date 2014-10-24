machine_learning
================
You should create a report describing how you built your model, how you used cross validation, what you think the expected out of sample error is, and why you made the choices you did. You will also use your prediction model to predict 20 different test cases.  



### The goal of this project is to predict the manner in which the participating athletes did the excercise, using the data from accelerometer. 

##The first part of the project is reading the data into training and test sets and then further divide the training data into trating and validation sets.

library(caret); library(ggplot2)
train_csv <- read.csv("pml-training.csv", header = TRUE, stringsAsFactors = FALSE)
test_csv <- read.csv("pml-testing.csv", header = TRUE, stringsAsFactors = FALSE)

inTrain <- createDataPartition(train_csv$classe, p=0.70, list=FALSE)
training <- train_csv[inTrain,]
validation <- train_csv[-inTrain,]

### In the next stage, we need to clean the data, estimate missing values and select features or predictors. 
