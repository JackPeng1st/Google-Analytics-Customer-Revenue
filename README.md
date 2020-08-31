國立交通大學 (NCTU) 彭家祐

Data: 13 Variables (such as device, date,  geoNetwork…... )  train_v2.csv: 23GB   test_v2.csv: 7GB

Goal: Predict revenue per customer. For each Id in the test set, predict the value of the transactionRevenue variable. 

Metric: Results are evaluated by Root-Mean-Squared-Error (RMSE) between the predicted value and the true revenue.          

Training data: select 100000 data( from train_v2.csv)，then delete the rows that missing transactionRevenue and select useful columns to build model
Test data: select 5000 data(from test_v2.csv)，then delete the rows that missing transactionRevenue 

data sicence competition website: https://www.kaggle.com/c/ga-customer-revenue-prediction/overview

My presentation file: https://docs.google.com/presentation/d/1nmFX-sJLfbjQqNoVo2naV5R_XGxMUHCU/edit#slide=id.p1
