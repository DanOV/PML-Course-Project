PML-Course-Project
==================
Practical Machine Learning: WriteUp (DOV)
========================================================

The dataset was "cleaned" before load it in R. Empty cells were substituted by NA's and the first column was eliminated for both csv files.

```{r}
## Load DataSet and RData: ##
load("~/Desktop/Practical Machine Learning/Peer Assessment/.RData")
#  train = read.csv2("pml-training-clean.csv", head=T, check.names=T, dec=".")
# head(train)
# colnames(train)
# summary(train)
summary(train$classe)
# str(train)

## Remove all variables with lots of NA's
# newTrain = train[,colSums(is.na(train))<100]

```

Random Forest:
========================================================
The Machine Learning algorithm choosed for this analysis is a random forest. The control was performed using OutOfBag (OOB) methodology, which leaves 1/3 of the sample during each iteration in order to obtain unbiased estimations for the classification trees. OOB also is used to get estimations of variables importance.

```{r}
## 1. Random Forest ####
## Select the variables that separates better between the levels of 'classe'.

library(caret)
# set.seed(020304)
# rf1 = train(classe~., data=newTrain, method=rf, trControl = trainControl(method=oob))
print(varImp(rf1, value=gcv))
```

Variable importance plot:
```{r fig.width=10, fig.height=16}
plot(varImp(rf1))
```

Final model:
```{r}
rf1.Final = rf1$finalModel
rf1.Final ## OOB error rate: 0.04%

## Prediciton vs Observed Table: ##
table(newTrain$classe,rf1.Final$predicted)
round(prop.table(table(newTrain$classe,rf1.Final$predicted),1),4)
```


Prediction from test file:
========================================================
```{r}
## 3. Prediction from test file ####
test = read.csv2("pml-testing-clean.csv", head=T, check.names=T, dec=".")
# summary(test)

answers = predict(rf1, newdata=test)
```

