machine_learning
================
 The goal of this project is to predict the manner in which the participating athletes did the excercise, using the data from accelerometer. 

The first part of the project is reading the data into training and test sets.

library(caret); library(ggplot2)
train_csv <- read.csv("pml-training.csv", header = TRUE, stringsAsFactors = FALSE)
test_csv <- read.csv("pml-testing.csv", header = TRUE, stringsAsFactors = FALSE)


In the next stage, we need to ispect and clean the data, estimate missing values and select features or predictors. There are 160 columns in the training data set, and it is important to clean the data and select the best predictors.

Ater inspecting and expoloring the data, we see that some columns are not numeric values. We select the columns in the training set that have numeric values (There are 97 columns with numeric values). We choose the same columns in the test set. 

nums <- sapply(trainData, is.numeric)
trainData1 <- trainData[ , nums]
validation1 <- validation1[,nums]
testData1 <- testData[,nums]

Also, we need to remove the columns with zero variance, as they are not suitable predictors.
nsv <- nearZeroVar(trainData1, saveMetrics = TRUE)
nsv <- nearZeroVar(trainData1, saveMetrics = FALSE)
trainData1 <- trainData1[,-nsv]
testData11 <- testData1[,-nsv]

After removing the zero variance columns, we are left with 83 columns. We see that there are a lot of missing values in the data, we impute the missing values using knnImpute after centering and re-scaling the features. Also, we perform principal component analysis to reduce the dimensionality and select principal components that remove 90% of the variance from the data. 

pp <- preProcess (trainData1, method = c("center","scale","knnImpute", "pca"), thresh=0.9)
training <- predict(pp, trainData1)
testing <- predict(pp, testData1)

#### Model bulding
We need to preict based on the classe variables. First we convert them to factors. Then we apply the classification algorithms decision tree and random forest for prediction. First we convert the classe variables into factors.
training$classe <- as.factor(trainData$classe). Then we select 10-fold cross validation, using the trainControl function in the caret package. 

fitControl <- trainControl(## 10-fold CV,
  method = "repeatedcv",
  number = 10,
  ## repeated ten times
  repeats = 10)

modFit1 <- train(classe ~., data= training, method="rpart", trControl = fitControl)
modFit2 <- train(classe ~., data= training, method="rf", trControl = fitControl)

result1 <- predict(modFit1, testing)
result2 <- predict(modFit2, testing)


To determine the accuracy of the matrix, we obtain the confusion matrix.
confusionMatrix(predict(modFit, training), training$classe)
#missclass <- missClass((predict(modFit, training), training$classe)
missClass = function(values, prediction) {
  sum(prediction != values)/length(values)
}              
