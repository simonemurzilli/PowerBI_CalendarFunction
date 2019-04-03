## 1) Function to create a UI to generate a Calendar 

The function is called *CalendarFunction* and it is invoked in lates step

```
let CreateDateTable = (StartDate as date, EndDate as date, optional Culture as nullable text) as table =>
  let
    DayCount = Duration.Days(Duration.From(EndDate - StartDate)) +1,
    Source = List.Dates(StartDate,DayCount,#duration(1,0,0,0)),
    TableFromList = Table.FromList(Source, Splitter.SplitByNothing()),
    ChangedType = Table.TransformColumnTypes(TableFromList,{{"Column1", type date}}),
    RenamedColumns = Table.RenameColumns(ChangedType,{{"Column1", "Date"}}),
    InsertYear = Table.AddColumn(RenamedColumns, "Year", each Date.Year([Date]), type number),
    InsertQuarter = Table.AddColumn(InsertYear, "QuarterOfYear", each Date.QuarterOfYear([Date]), type number),
    InsertMonth = Table.AddColumn(InsertQuarter, "MonthOfYear", each Date.Month([Date]), type number),
    InsertDay = Table.AddColumn(InsertMonth, "DayOfMonth", each Date.Day([Date]), type number),
    InsertDayInt = Table.AddColumn(InsertDay, "DateInt", each [Year] * 10000 + [MonthOfYear] * 100 + [DayOfMonth], type number),
    InsertMonthName = Table.AddColumn(InsertDayInt, "MonthName", each Date.ToText([Date], "MMMM", Culture), type text),
    InsertCalendarMonth = Table.AddColumn(InsertMonthName, "MonthInCalendar", each (try(Text.Range([MonthName],0,3)) otherwise [MonthName]) & " " & Number.ToText([Year])),
    InsertInsertCalendarMonthInt = Table.AddColumn(InsertCalendarMonth, "MonthInCalendarInt", each [Year] * 100 + [MonthOfYear], type number),
    InsertCalendarQtr = Table.AddColumn(InsertInsertCalendarMonthInt, "QuarterInCalendar", each "Q" & Number.ToText([QuarterOfYear]) & " " & Number.ToText([Year])),
    InsertCalendarQtrInt = Table.AddColumn(InsertCalendarQtr,  "QuarterInCalendarInt", each [Year] * 10 + Date.QuarterOfYear([Date]), type number),
    InsertDayWeek = Table.AddColumn(InsertCalendarQtrInt, "DayInWeek", each Date.DayOfWeek([Date])),
    InsertDayName = Table.AddColumn(InsertDayWeek, "DayOfWeekName", each Date.ToText([Date], "dddd", Culture), type text),
    InsertWeekEnding = Table.AddColumn(InsertDayName, "WeekEnding", each Date.EndOfWeek([Date]), type date),
    InsertWorkingDay = Table.AddColumn(InsertWeekEnding, "WorkingDay", each if (Date.DayOfWeek([Date]) = 5 or Date.DayOfWeek([Date]) = 6) then 0 else 1, type number)
  in
    InsertWorkingDay
in
  CreateDateTable
```


## 2) Function to get the Min Max from a Table

The example use a table called *TABLENAME* (replace it)
The Table is used in step 3) as *ActualDates* so in case rename the created table.

```
let
    Fn = (TableName as table, ColumnName as text) =>
	let
	    v = List.Min(Table.Column(TableName, ColumnName))       
	in
	    v
in
    Fn
 ``` 
 
 
 ```
let
    Fn = (TableName as table, ColumnName as text) =>
	let
	    v = List.Max(Table.Column(TableName, ColumnName))       
	in
	    v
in
    Fn
 ``` 
 
 ## 3) Generate a Calendar Table using the Min and Max Date from a Table
 
This step use the 1) *CalendarFunction* to create the calendar using the *FnMin* and *FnMax* to find the date ranges
 
 ```
let
    Source = FnCalendar( FnMin(TABLENAME, "Date"),  FnMax(TABLENAME, "Date"), null),
    #"Changed Type" = Table.TransformColumnTypes(Source,{{"MonthOfYear", Int64.Type}, {"QuarterOfYear", Int64.Type}, {"Year", Int64.Type}, {"DayOfMonth", Int64.Type}, {"DateInt", Int64.Type}, {"DayInWeek", Int64.Type}})
in
    #"Changed Type"
    
 ```


