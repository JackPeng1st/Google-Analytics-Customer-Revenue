https://www.kaggle.com/c/ga-customer-revenue-prediction/overview

https://docs.google.com/presentation/d/1yRQrmj1-dbWA3ERlK4z4zhygvj0KbKXH0NYS6L6yPeA/edit#slide=id.g5c19a00621_0_119

#train=read.csv("train_v2.csv",sep=",",na.strings = "",nrows = 6)
#train=read.csv("train_v2.csv",sep=",",na.strings = "",nrows = 1000)[,-c(2,4,6,7,9,10)]

#sapply(train,function(x) sum(is.na(x)))


#b=read.csv("train_v2.csv",sep=",",na.strings = "",nrows = 5000)[,1]

#col_name=colnames(test)
#test=read.csv("test_v2.csv",sep=",",na.strings = "",skip=100000,nrows=10,col.names = col_name)[,'date']

library(tidyverse)
library(jsonlite)
library(gtools)
library(DMwR)
library(randomForest)
library(rpart)
library(e1071)
train_all=data.frame()

train=read.csv("train_v2.csv",sep=",",na.strings = "",nrows = 1)
col_name=colnames(train)

for (i in 0:1){

  train=read.csv("train_v2.csv",sep=",",na.strings = "",nrows = 1000,skip=(1000*i),col.names = col_name)

  tr_device <-   fromJSON(paste("[", paste(train$device, collapse = ","), "]"),flatten = T)
  tr_geoNetwork <- fromJSON(paste("[", paste(train$geoNetwork, collapse = ","), "]") ,flatten = T)
  tr_totals <-  fromJSON(paste("[", paste(train$totals, collapse = ","), "]"),flatten = T)
  tr_trafficSource <-fromJSON( paste("[", paste(train$trafficSource, collapse = ","), "]"),flatten = T)
  col=cbind(tr_device, tr_geoNetwork, tr_totals, tr_trafficSource)
  train=select(train,-device, -geoNetwork, -totals, -trafficSource,-hits,-customDimensions)
  train=cbind(train,col)
  train_all=smartbind(train_all,train)#gtools
  train_all=subset(train_all,is.na(transactionRevenue)==F&is.na(totalTransactionRevenue)==F)

}
train_all=select(train_all,-bounces,-newVisits,-timeOnSite,-adContent ,-referralPath,
                 -isTrueDirect,- adwordsClickInfo.page,- keyword ,
                                      -adwordsClickInfo.slot,-adwordsClickInfo.gclId,
                 - adwordsClickInfo.adNetworkType,-adwordsClickInfo.isVideoAd )#hits 的型態奇怪，不知是否可用
train_all=centralImputation(train_all)
#把歸類錯誤的變數改正確
train_all$visits=as.integer(train_all$visits)
train_all$hits=as.integer(train_all$hits)
train_all$pageviews=as.integer(train_all$pageviews)
train_all$transactions=as.integer(train_all$transactions)
train_all$sessionQualityDim=as.integer(train_all$sessionQualityDim)
train_all$transactions=as.integer(train_all$transactions)
train_all$transactionRevenue=as.integer(train_all$transactionRevenue)
train_all$totalTransactionRevenue=as.integer(train_all$totalTransactionRevenue)


chr <- train_all[,sapply(train_all,is.character)]
int <- train_all[,sapply(train_all,is.integer)]
fac <- train_all[,sapply(train_all,is.factor)]
col=c(colnames(chr),colnames(int),colnames(fac))
col_all=colnames(train_all)
no_type<- subset(train_all,select=col_all[!col_all %in% c(col)])
fullVisitorId=data.frame(as.character(no_type$fullVisitorId))
colnames(fullVisitorId)="fullVisitorId"
isMobile=data.frame(as.factor(no_type$isMobile))
colnames(isMobile)="isMobile"
no_type=cbind(fullVisitorId,isMobile)
chr=chr %>% lapply(as.factor) %>% as.data.frame()
train_all=cbind(chr,fac,int,no_type)
train_data=subset(train_all,select=-fullVisitorId)




model.rf <- randomForest(transactionRevenue~.,data=train_data,ntree=500,proximity=TRUE)#超過53個類別，不能用
#tree.model=rpart(formula=transactionRevenue~.,data=train_all,control = rpart.control(cp = 0.001))
svm_model<-svm(transactionRtevenue~., data=train_data, cost = 3)
#Test data
test_all=data.frame()

test=read.csv("test_v2.csv",sep=",",na.strings = "",nrows = 1)
col_name=colnames(test)

for (i in 0){

  test=read.csv("test_v2.csv",sep=",",na.strings = "",nrows = 1000,skip=(1000*i),col.names = col_name)

  tr_device <-   fromJSON(paste("[", paste(test$device, collapse = ","), "]"),flatten = T)
  tr_geoNetwork <- fromJSON(paste("[", paste(test$geoNetwork, collapse = ","), "]") ,flatten = T)
  tr_totals <-  fromJSON(paste("[", paste(test$totals, collapse = ","), "]"),flatten = T)
  tr_trafficSource <-fromJSON( paste("[", paste(test$trafficSource, collapse = ","), "]"),flatten = T)
  col=cbind(tr_device, tr_geoNetwork, tr_totals, tr_trafficSource)
  test=select(test,-device, -geoNetwork, -totals, -trafficSource,-hits)
  test=cbind(test,col)
  test_all=smartbind(test_all,test)#gtools
  test_all=subset(test_all,is.na(transactionRevenue)==F&is.na(totalTransactionRevenue)==F)

}
test_all=select(test_all,-bounces,-newVisits,-timeOnSite,-adContent ,-referralPath,
                 -isTrueDirect,- adwordsClickInfo.page,- keyword ,
                 -adwordsClickInfo.slot,-adwordsClickInfo.gclId,
                 - adwordsClickInfo.adNetworkType,-adwordsClickInfo.isVideoAd )
test_all=centralImputation(test_all)

test_all$visits=as.integer(test_all$visits)
test_all$hits=as.integer(test_all$hits)
test_all$pageviews=as.integer(test_all$pageviews)
test_all$transactions=as.integer(test_all$transactions)
test_all$sessionQualityDim=as.integer(test_all$sessionQualityDim)
test_all$transactions=as.integer(test_all$transactions)
test_all$transactionRevenue=as.integer(test_all$transactionRevenue)
test_all$totalTransactionRevenue=as.integer(test_all$totalTransactionRevenue)


chr <- test_all[,sapply(test_all,is.character)]
int <- test_all[,sapply(test_all,is.integer)]
fac <- test_all[,sapply(test_all,is.factor)]
col=c(colnames(chr),colnames(int),colnames(fac))
col_all=colnames(test_all)
no_type<- subset(test_all,select=col_all[!col_all %in% c(col)])
fullVisitorId=data.frame(as.character(no_type$fullVisitorId))
colnames(fullVisitorId)="fullVisitorId"
isMobile=data.frame(as.factor(no_type$isMobile))
colnames(isMobile)="isMobile"
no_type=cbind(fullVisitorId,isMobile)
chr=chr %>% lapply(as.factor) %>% as.data.frame()
test_all=cbind(chr,fac,int,no_type)
test_data=subset(test_all,select=-fullVisitorId)

test=select(test_data,-transactionRevenue)
#prediction=predict(tree.model,test)
prediction_rf=predict(model.rf ,test)
