
## Function to get the Min and Max dates from a Table

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
 
 ## Generate a Calendar Table using the Min and Max Date from a Table
 
 ```
 let
    Source = Query(List.Min(Table.Column(Dates, "MinDate")), List.Min(Table.Column(Dates, "MaxDate")), null),
    #"Changed Type" = Table.TransformColumnTypes(Source,{{"MonthOfYear", Int64.Type}, {"QuarterOfYear", Int64.Type}, {"Year", Int64.Type}, {"DayOfMonth", Int64.Type}, {"DateInt", Int64.Type}, {"DayInWeek", Int64.Type}})
in
    #"Changed Type"
 ```
