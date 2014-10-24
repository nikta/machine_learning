machine_learning
================
### The goal of this project is to predict the manner in which the participating athletes did the excercise, using the data from accelerometer. 

##The first part of the project is reading the data into training and test sets and then further divide the training data into trating and validation sets.

library(caret); library(ggplot2)
train_csv <- read.csv("pml-training.csv", header = TRUE, stringsAsFactors = FALSE)
test_csv <- read.csv("pml-testing.csv", header = TRUE, stringsAsFactors = FALSE)

inTrain <- createDataPartition(train_csv$classe, p=0.70, list=FALSE)
trainData <- train_csv[inTrain,]
validation <- train_csv[-inTrain,]

### In the next stage, we need to ispect and clean the data, estimate missing values and select features or predictors. There are 160 columns in each of training, validation data sets, it is important to clean the data and select the best predictors.

Ater inspecting and expoloring the data, we see that some columns are not numeric values. We select the columns in the training and validation sets that have numeric values. We select the same columns in the test set. There are 97 columns with numeric values.

nums <- sapply(trainData, is.numeric)
trainData1 <- trainData[ , nums]
validation1 <- validation1[,nums]
testData1 <- testData[,nums]

##Also, we need to remove the columns with zero variance, as they are not suitable predictors.
###remove the covariates with zero variation
nsv <- nearZeroVar(trainData1, saveMetrics = TRUE)
nsv <- nearZeroVar(trainData1, saveMetrics = FALSE)
trainData1 <- trainData1[,-nsv]
validation1 <- validation[,-nsv]
testData11 <- testData1[,-nsv]

##After removing the zero variance columns, we are left with 83 columns. There are a lot of missing values in the data, we impute the missing values using knnImpute after cenering and re-scaling the features. Also, we perform principal component analysis that remove 90% of teh variance from the data. 

pp <- preProcess (trainData1, method = c("center","scale","knnImpute", "pca"), thresh=0.9)
training <- predict(pp, trainData1)
validatiing <- predict(pp, validation1)
testing <- predict(pp, testData1)

## We nee to preict based on teh classe variables. First we convert them to factors. Then we apply the training algorithms decision tree and random forest for prediction.
training$classe <- as.factor(trainData$classe)

We select 10-fold cross validation, using the trainControl function in the caret package. 

fitControl <- trainControl(## 10-fold CV,
  method = "repeatedcv",
  number = 10,
  ## repeated ten times
  repeats = 10)

modFit1 <- train(classe ~., data= training, method="rpart", trControl = fitControl)
#modFit2 <- train(classe ~., data= training, method="rf", trControl = fitControl)

result1 <- predict(modFit1, testing)
result2 <- predict(modFit2, testing)
missClass = function(values,prediction){sum(((prediction > 0.5)*1) != values)/length(values)}


confusionMatrix(predict(modFit, training), training$classe)
#missclass <- missClass((predict(modFit, training), training$classe)
missClass = function(values, prediction) {
  sum(prediction != values)/length(values)
}              
