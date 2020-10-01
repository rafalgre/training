# training
Training materials


Link to API:
https://catalogue.data.govt.nz/api/3/action/datastore_search?resource_id=20b7c271-fd5a-4c9e-869b-481a0e2453cd

M Code for custom API

```
let
    Source = Web.BrowserContents("https://catalogue.data.govt.nz/api/3/action/datastore_search_sql?sql=SELECT%20*%20FROM%2220b7c271-fd5a-4c9e-869b-481a0e2453cd%22"),
    Table = Html.Table(Source, {{"Results", "pre"}}),
    ConvertToJson=Json.Document(Table{0}[Results]),
    #"Converted to Table" = Record.ToTable(ConvertToJson),
    Value = #"Converted to Table"{2}[Value],
    records = Value[records],
    #"Converted to Table1" = Table.FromList(records, Splitter.SplitByNothing(), null, null, ExtraValues.Error),
    #"Expanded Column1" = Table.ExpandRecordColumn(#"Converted to Table1", "Column1", {"Māori", "Education_Region", "Decile", "Telephone", "Other", "Pacific", "Latitude", "International", "Email", "Area_Unit", "Add2_City", "Fax", "Regional_Council", "Add2_Suburb", "Col_Id", "European", "Territorial_Authority", "Ward", "Māori_Electorate", "Add2_Line1", "Org_Name", "Definition", "Add1_City", "URL", "Urban_Area", "Col_Name", "Longitude", "CoEd_Status", "Contact1_Name", "Local_Office_Name", "General_Electorate", "Total", "Roll_Date", "_id", "_full_text", "Add1_Suburb", "Authority", "Org_Type", "Asian", "MELAA", "Add1_Line1", "Add2_Postal_Code", "School_Id"}, {"Māori", "Education_Region", "Decile", "Telephone", "Other", "Pacific", "Latitude", "International", "Email", "Area_Unit", "Add2_City", "Fax", "Regional_Council", "Add2_Suburb", "Col_Id", "European", "Territorial_Authority", "Ward", "Māori_Electorate", "Add2_Line1", "Org_Name", "Definition", "Add1_City", "URL", "Urban_Area", "Col_Name", "Longitude", "CoEd_Status", "Contact1_Name", "Local_Office_Name", "General_Electorate", "Total", "Roll_Date", "_id", "_full_text", "Add1_Suburb", "Authority", "Org_Type", "Asian", "MELAA", "Add1_Line1", "Add2_Postal_Code", "School_Id"}),
    #"Reordered Columns" = Table.ReorderColumns(#"Expanded Column1",{"_id", "School_Id", "Org_Name", "Org_Type", "Authority", "Education_Region", "Māori",  "Pacific", "European", "International", "Asian", "MELAA", "Other", "Total", "Telephone", "Decile", "Latitude", "Longitude", "Email", "Area_Unit", "Add2_City", "Fax", "Regional_Council", "Add2_Suburb", "Col_Id", "Territorial_Authority", "Ward", "Māori_Electorate", "Add2_Line1", "Definition", "Add1_City", "URL", "Urban_Area", "Col_Name", "CoEd_Status", "Contact1_Name", "Local_Office_Name", "General_Electorate", "Roll_Date", "_full_text", "Add1_Suburb",  "Add1_Line1", "Add2_Postal_Code"}),
    #"Changed Type" = Table.TransformColumnTypes(#"Reordered Columns",{{"Māori", Int64.Type}, {"Decile", Int64.Type}, {"Pacific", Int64.Type}, {"European", Int64.Type}, {"International", Int64.Type}, {"Other", Int64.Type}, {"Asian", Int64.Type}, {"Total", Int64.Type}, {"Latitude", type number}, {"Longitude", type number}, {"MELAA", Int64.Type}}),
    #"Removed Columns" = Table.RemoveColumns(#"Changed Type",{"_full_text"}),
    #"Renamed Columns" = Table.RenameColumns(#"Removed Columns",{{"Area_Unit", "Area Unit"}, {"Asian", "Asian"}, {"CoEd_Status", "CoEd Status"}, {"Col_Name", "Col Name"}, {"Education_Region", "Education Region"}, {"General_Electorate", "General Electorate"}, {"Local_Office_Name", "Local Office Name"}, {"Roll_Date", "Roll Date"}, {"Urban_Area", "Urban Area"}, {"Māori_Electorate", "Māori Electorate"}, {"Territorial_Authority", "Territorial Authority"}, {"Regional_Council", "Regional Council"}, {"Org_Name", "Organization Name"}, {"Org_Type", "Organization Type"}, {"Add1_City", "City"}, {"School_Id", "School Number"}})
in
    #"Renamed Columns"
    
```


Custom formatting for Date dimension

    Date: m/dd/yyyy (1/14/2008), used as a column to mark as date table
    Year: yyyy (2008)
    Year Month: mmm yyyy (Jan 2008)


```
Date =
VAR FirstYear =           -- Customizes the first year to use
    YEAR ( MIN ( Sales[Order Date]  ) )
RETURN
ADDCOLUMNS (
    FILTER (
        CALENDARAUTO (),
        YEAR ( [Date] ) >= FirstYear
    ),
    "Year", DATE ( YEAR ( [Date] ), 12, 31 ),
    "Year Month", EOMONTH ( [Date], 0 ),
    "Month", FORMAT ( [Date], "mmm" ),
    "Month Number", MONTH ( [Date] ),
    "Day of Week", FORMAT ( [Date], "ddd" ),
    "Day of Week Number", WEEKDAY ( [Date], 1 )
)
```

