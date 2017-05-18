## Create a naive simple dictionary on a dataset

It creates an additional column with true/false values for each row. 

```
# 'dataset' holds the input data for this script

# It produce a new colum "NEW_COLUMN" using regular expressions on a column in the dataset i.e. dataset$COLUMN
dataset$NEW_COLUMN <- grepl('\\bWORD1\\b|\\bWORD2\\b', dataset$COLUMN)

output <- dataset
```
