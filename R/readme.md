
# Create a naive simple dictionary on a dataset using regular expressions

```R
# 'dataset' holds the input data for this script

# It produce a new colum "NEW_COLUMN" using regular expressions analyzing a column in the dataset i.e. dataset$COLUMN
dataset$NEW_COLUMN <- grepl('\\bWORD1\\b|\\bWORD2\\b', dataset$COLUMN)

# output is the new "output" created in PowerBI
output <- dataset
```
The new column will contain TRUE or FALSE values, which can be used to filter the table.

# Other usage

The column(s) could be pivoted, filtered when they are TRUE, and if connected to another table could be used as *slicers* in PowerBI
