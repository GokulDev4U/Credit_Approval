# Credit_Approval
Data Description
The client will send data in multiple sets of files in batches at a given location. Data will contain different classes of Credit Approval and 15 columns of different values.
"Class" column will have two unique values “+’’ & “-”

Apart from training files, we also require a "schema" file from the client, which contains all the relevant information about the training files such as:
 Number of Columns, Name of the Columns, and their datatype.
 
Data Validation 
In this step, we perform different sets of validation on the given set of training files.  

1. Number of Columns - We validate the number of columns present in the files, and if it doesn't match with the value given in the schema file, then the file is moved to "Rejected folder" else moved to “Validate folder”
For training: training_data_reject,training_data_validate
For predicting: predicting_data_reject,predicting_data_validate

2. Name of Columns - The name of the columns is validated and should be the same as given in the schema file. If not, then the file is moved to "Rejected folder".

3. The datatype of columns - The datatype of columns is given in the schema file. This is validated when we insert the files into Database. If the datatype is wrong, then the file is moved to "Rejected folder".

4.Null values in columns - If any of the columns in a file have all the values as NULL or missing, we discard such a file and move it to "Rejected folder".



Data Insertion in Database
 
1) Database Creation and connection - Create a database with the given name passed. If the database is already created, open the connection to the database. 
2) Table creation in the database - Table with name - "Train Data", is created in the database for inserting the files in the "Validate Folder" based on given column names and datatype in the schema file. If the table is already present, then the new table is not created and new files are inserted in the already present table as we want training to be done on new as well as old training files.     
3) Insertion of files in the table - All the files in the "Validate Folder" are inserted in the above-created table. If any file has invalid data type in any of the columns, the file is not loaded in the table and is moved to "Rejected Folder".
 
Model Training 
1) Data Export from Db - The data in a stored database is exported as a CSV file to be used for model training.
2) Data Preprocessing   
   a) Drop columns not useful for training the model. Such columns were selected while doing the EDA.
   b) Replace the invalid values(‘?’) with numpy “nan” so we can use imputer on such values.
   c) Encode the categorical values
   d) Check for null values in the columns. If present, impute the null values using the KNN imputer.
e)top four feature is selected with Selectkbest & chi2
3) Model Selection - After feature selection, we find the best model for each cluster. We are using two algorithms, "Random Forest" and "Xgboost". For each cluster, both the algorithms are passed with the best parameters derived from GridSearch. We calculate the AUC scores for both models and select the model with the best score. Similarly, the model is selected for each cluster. All the models for every cluster are saved for use in prediction. 
 
Prediction Data Description
 The client will send data in multiple sets of files in batches at a given location. Data will contain different classes of Credit Approval and 15 columns of different values.
Apart from prediction files, we also require a "schema" file from the client, which contains all the relevant information about the training files such as:
 Number of Columns, Name of the Columns, and their datatype.

Data Validation  
In this step, we perform different sets of validation on the given set of training files.  
1.Number of Columns - We validate the number of columns present in the files, and if it doesn't match with the value given in the schema file, then the file is moved to "Rejected folder" else moved to “Validate folder”
For training: training_data_reject,training_data_validate
For predicting: predicting_data_reject,predicting_data_validate

2. Name of Columns - The name of the columns is validated and should be the same as given in the schema file. If not, then the file is moved to "Rejected folder".

    3.The datatype of columns - The datatype of columns is given in the schema file. This is validated when we insert the files into Database. If the datatype is wrong, then the file is moved to "Rejected folder".

5. Null values in columns - If any of the columns in a file have all the values  as NULL or missing, we discard such a file and move it to "Rejected folder". 

Data Insertion in Database 

1) Database Creation and connection - Create a database with the given name passed. If the database is already created, open the connection to the database. 
2) Table creation in the database - Table with name - "Predict Data", is created in the database for inserting the files in the "Validate Folder" based on given column names and datatype in the schema file. If the table is already present, then the new table is not created and new files are inserted in the already present table as we want training to be done on new as well as old training files.     
3) Insertion of files in the table - All the files in the "Validate Folder" are inserted in the above-created table. If any file has invalid data type in any of the columns, the file is not loaded in the table and is moved to "Rejected Folder".


Prediction 
 
1) Data Export from Db - The data in the stored database is exported as a CSV file to be used for prediction.
2) Data Preprocessing   
   a) Drop columns not useful for training the model. Such columns were selected while doing the EDA.
   b) Replace the invalid values with numpy “nan” so we can use imputer on such values.
   c) Encode the categorical values
   d) Check for null values in the columns. If present, impute the null values using the KNN imputer.
e)top four feature is selected with Selectkbest & chi2
3) Prediction -  the best model is loaded and is used to predict the data .
4) Once the prediction is made, the predictions along with the original names before label encoder are saved in a CSV file at a given location and the location is returned to the client.
