## 1) Function to create a UI to generate a Calendar 

The function is called *CalendarFunction* and it is invoked in lates step

```
let CreateDateTable = (StartDate as date, EndDate as date, optional Culture as nullable text) as table =>
  let
    DayCount = Duration.Days(Duration.From(EndDate - StartDate)),
    Source = List.Dates(StartDate,DayCount,#duration(1,0,0,0)),
    TableFromList = Table.FromList(Source, Splitter.SplitByNothing()),
    ChangedType = Table.TransformColumnTypes(TableFromList,{{"Column1", type date}}),
    RenamedColumns = Table.RenameColumns(ChangedType,{{"Column1", "Date"}}),
    InsertYear = Table.AddColumn(RenamedColumns, "Year", each Date.Year([Date])),
    InsertQuarter = Table.AddColumn(InsertYear, "QuarterOfYear", each Date.QuarterOfYear([Date])),
    InsertMonth = Table.AddColumn(InsertQuarter, "MonthOfYear", each Date.Month([Date])),
    InsertDay = Table.AddColumn(InsertMonth, "DayOfMonth", each Date.Day([Date])),
    InsertDayInt = Table.AddColumn(InsertDay, "DateInt", each [Year] * 10000 + [MonthOfYear] * 100 + [DayOfMonth]),
    InsertMonthName = Table.AddColumn(InsertDayInt, "MonthName", each Date.ToText([Date], "MMMM", Culture), type text),
    InsertCalendarMonth = Table.AddColumn(InsertMonthName, "MonthInCalendar", each (try(Text.Range([MonthName],0,3)) otherwise [MonthName]) & " " & Number.ToText([Year])),
    InsertCalendarQtr = Table.AddColumn(InsertCalendarMonth, "QuarterInCalendar", each "Q" & Number.ToText([QuarterOfYear]) & " " & Number.ToText([Year])),
    InsertDayWeek = Table.AddColumn(InsertCalendarQtr, "DayInWeek", each Date.DayOfWeek([Date])),
    InsertDayName = Table.AddColumn(InsertDayWeek, "DayOfWeekName", each Date.ToText([Date], "dddd", Culture), type text),
    InsertWeekEnding = Table.AddColumn(InsertDayName, "WeekEnding", each Date.EndOfWeek([Date]), type date)
  in
    InsertWeekEnding
in
  CreateDateTable
```


## 2) Function to get the Min and Max dates from a Table 

The example use a table called *TABLENAME* (replace it)
The Table is used in step 3) as *ActualDates* so in case rename the created table.

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
 
 ## 3) Generate a Calendar Table using the Min and Max Date from a Table
 
This step use the 1) *CalendarFunction* to create the calendar from 2) Table *Dates*
 
 ```
 let
    Source = CalendarFunction(List.Min(Table.Column(ActualDates, "MinDate")), List.Min(Table.Column(ActualDates, "MaxDate")), null),
    #"Changed Type" = Table.TransformColumnTypes(Source,{{"MonthOfYear", Int64.Type}, {"QuarterOfYear", Int64.Type}, {"Year", Int64.Type}, {"DayOfMonth", Int64.Type}, {"DateInt", Int64.Type}, {"DayInWeek", Int64.Type}})
in
    #"Changed Type"
 ```


