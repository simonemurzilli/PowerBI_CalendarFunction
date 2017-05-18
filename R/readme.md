
# output is the "ou## Create a naive simple dictionary on a dataset

```
# 'dataset' holds the input data for this script

# It produce a new colum "NEW_COLUMN" using regular expressions analyzing a column in the dataset i.e. dataset$COLUMN
dataset$NEW_COLUMN <- grepl('\\bWORD1\\b|\\bWORD2\\b', dataset$COLUMN)

# output is the new "output" created in PowerBI
output <- dataset
```
