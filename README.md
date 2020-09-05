國立交通大學 (NCTU) 彭家祐

Data: 13 Variables (such as device, date,  geoNetwork…... ) 
     But after processing the data, there are 59 varaibles.
     train_v2.csv: 23GB   test_v2.csv: 7GB

Goal: Predict revenue per customer. For each Id in the test set, predict the value of the transactionRevenue variable. 

Metric: Results are evaluated by Root-Mean-Squared-Error (RMSE) between the predicted value and the true revenue.          

Training data: select 100000 data( from train_v2.csv)，then delete the rows that missing transactionRevenue and select useful columns to build model
Test data: select 5000 data(from test_v2.csv)，then delete the rows that missing transactionRevenue 

Methods: Decision Tree, Random Forest, etc...

Result: RMSE predicted by Decision Tree= 0.899
        RMSE predicted by Random Forest= 0.533
    --> Making 

data sicence competition website: https://www.kaggle.com/c/ga-customer-revenue-prediction/overview

My presentation file: https://docs.google.com/presentation/d/1nmFX-sJLfbjQqNoVo2naV5R_XGxMUHCU/edit#slide=id.p1
