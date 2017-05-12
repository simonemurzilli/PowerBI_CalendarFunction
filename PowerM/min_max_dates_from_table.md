
# Function to get the Min and Max dates from a Table

```
let
    FnMinMaxDate = (TableName as table, StartDate as text, EndDate as text) =>
	let
	    DatesPerTable = Table.FromRecords({
	         [MinDate = List.Min(Table.Column(TableName, StartDate)), 
	          MaxDate = List.Max(Table.Column(TableName, EndDate))]
	        }, 
	        type table [MinDate = datetime, MaxDate = datetime])
	in
	    DatesPerTable,
	    #"Invoked Function" = FnMinMaxDate(TABLENAME, "StartDate", "EndDate")
in
    #"Invoked Function"
 ``` 
