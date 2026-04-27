# 

## Table of Content
[Problem Statement](#problem-statement)

[Data Source](#data-source)

[Tools](#tools)

[Dashboard](#dashboard)

[Main Insights](#main-insights)

### Problem Statement 

Netflix wants to better understand which movie they should produce next, including the most suitable actors and directors. We have a dataset containing movie budgets, box office performance, actors, directors, and genres. Your task is to build an Excel dashboard that provides insights into this dataset. The dashboard should help identify:

- The best-performing actors
- The top movies based on box office metrics
- Director performance
- Genre trends
- Seasonal patterns in movie performance
- Any additional insights that can guide future production decisions
  
The final dashboard should be clear, interactive, and visually compelling, enabling Netflix to make data-driven decisions.

### Data Source

The primary dataset used for this analysis is the "Movie Data.xlsx" file, containing detailed information about each movie's performance (box office and budget), actors, directors and genres. You can download the original data source here: [Movie Dataset Excel file](https://github.com/user-attachments/files/27136454/Movie.Data.xlsx)

### Tools

- **Power Query** for Data Cleaning and Transformation 
- **Excel** for Data Visualization

### Dashboard

<img width="1164" height="1220" alt="image" src="https://github.com/user-attachments/assets/08e40475-ee99-4ace-847a-9e0fc4715509" />


### M Code
```
let
  Source = Excel.Workbook(File.Contents("/Users/kseniiasotsenko/Desktop/DA_HW/Movies_Data_Homework.xlsx"), null, true),
  #"Navigation 1" = Source{[Item = "Movie Data", Kind = "Sheet"]}[Data],
  #"Promoted headers" = Table.PromoteHeaders(#"Navigation 1", [PromoteAllScalars = true]),
  #"Changed column type" = Table.TransformColumnTypes(#"Promoted headers", {{"Movie Title", type text}, {"Release Date", type date}, {"Wikipedia URL", type text}, {"Genre_First_ID", Int64.Type}, {"Genre_Second_ID", Int64.Type}, {"Director_First_ID", Int64.Type}, {"Cast_First_ID", Int64.Type}, {"Cast_Second_ID", Int64.Type}, {"Cast_Third_ID", Int64.Type}, {"Cast_Fourth_ID", Int64.Type}, {"Cast_Fifth_ID", Int64.Type}, {"Budget ($)", Int64.Type}, {"Box Office Revenue ($)", type number}}),
  #"Removed columns" = Table.RemoveColumns(#"Changed column type", {"Column14", "Column15", "Column18", "Column17", "Column16", "Column19", "Column20", "Column21"}),
  #"Merged queries" = Table.NestedJoin(#"Removed columns", {"Genre_First_ID"}, Genres, {"ID"}, "Genres", JoinKind.LeftOuter),
  #"Expanded Genres" = Table.ExpandTableColumn(#"Merged queries", "Genres", {"Genre"}, {"Genre"}),
  #"Reordered columns" = Table.ReorderColumns(#"Expanded Genres", {"Movie Title", "Release Date", "Wikipedia URL", "Genre_First_ID", "Genre", "Genre_Second_ID", "Director_First_ID", "Cast_First_ID", "Cast_Second_ID", "Cast_Third_ID", "Cast_Fourth_ID", "Cast_Fifth_ID", "Budget ($)", "Box Office Revenue ($)"}),
  #"Renamed columns" = Table.RenameColumns(#"Reordered columns", {{"Genre", "Genre_First"}}),
  #"Merged queries 1" = Table.NestedJoin(#"Renamed columns", {"Genre_Second_ID"}, Genres, {"ID"}, "Genres", JoinKind.LeftOuter),
  #"Expanded Genres 1" = Table.ExpandTableColumn(#"Merged queries 1", "Genres", {"Genre"}, {"Genre"}),
  #"Reordered columns 1" = Table.ReorderColumns(#"Expanded Genres 1", {"Movie Title", "Release Date", "Wikipedia URL", "Genre_First_ID", "Genre_First", "Genre_Second_ID", "Genre", "Director_First_ID", "Cast_First_ID", "Cast_Second_ID", "Cast_Third_ID", "Cast_Fourth_ID", "Cast_Fifth_ID", "Budget ($)", "Box Office Revenue ($)"}),
  #"Renamed columns 1" = Table.RenameColumns(#"Reordered columns 1", {{"Genre", "Genre_Second"}}),
  #"Merged queries 2" = Table.NestedJoin(#"Renamed columns 1", {"Director_First_ID"}, Directors, {"ID"}, "Directors", JoinKind.LeftOuter),
  #"Expanded Directors" = Table.ExpandTableColumn(#"Merged queries 2", "Directors", {"Director"}, {"Director"}),
  #"Reordered columns 2" = Table.ReorderColumns(#"Expanded Directors", {"Movie Title", "Release Date", "Wikipedia URL", "Genre_First_ID", "Genre_First", "Genre_Second_ID", "Genre_Second", "Director_First_ID", "Director", "Cast_First_ID", "Cast_Second_ID", "Cast_Third_ID", "Cast_Fourth_ID", "Cast_Fifth_ID", "Budget ($)", "Box Office Revenue ($)"}),
  #"Merged queries 3" = Table.NestedJoin(#"Reordered columns 2", {"Cast_First_ID"}, Actors, {"ID"}, "Actors", JoinKind.LeftOuter),
  #"Expanded Actors" = Table.ExpandTableColumn(#"Merged queries 3", "Actors", {"Actor"}, {"Actor"}),
  #"Reordered columns 3" = Table.ReorderColumns(#"Expanded Actors", {"Movie Title", "Release Date", "Wikipedia URL", "Genre_First_ID", "Genre_First", "Genre_Second_ID", "Genre_Second", "Director_First_ID", "Director", "Cast_First_ID", "Actor", "Cast_Second_ID", "Cast_Third_ID", "Cast_Fourth_ID", "Cast_Fifth_ID", "Budget ($)", "Box Office Revenue ($)"}),
  #"Renamed columns 2" = Table.RenameColumns(#"Reordered columns 3", {{"Actor", "Actor_First"}}),
  #"Merged queries 4" = Table.NestedJoin(#"Renamed columns 2", {"Cast_Second_ID"}, Actors, {"ID"}, "Actors", JoinKind.LeftOuter),
  #"Expanded Actors 1" = Table.ExpandTableColumn(#"Merged queries 4", "Actors", {"Actor"}, {"Actor"}),
  #"Reordered columns 4" = Table.ReorderColumns(#"Expanded Actors 1", {"Movie Title", "Release Date", "Wikipedia URL", "Genre_First_ID", "Genre_First", "Genre_Second_ID", "Genre_Second", "Director_First_ID", "Director", "Cast_First_ID", "Actor_First", "Cast_Second_ID", "Actor", "Cast_Third_ID", "Cast_Fourth_ID", "Cast_Fifth_ID", "Budget ($)", "Box Office Revenue ($)"}),
  #"Renamed columns 3" = Table.RenameColumns(#"Reordered columns 4", {{"Actor", "Actor_Second"}}),
  #"Merged queries 5" = Table.NestedJoin(#"Renamed columns 3", {"Cast_Third_ID"}, Actors, {"ID"}, "Actors", JoinKind.LeftOuter),
  #"Expanded Actors 2" = Table.ExpandTableColumn(#"Merged queries 5", "Actors", {"Actor"}, {"Actor"}),
  #"Reordered columns 5" = Table.ReorderColumns(#"Expanded Actors 2", {"Movie Title", "Release Date", "Wikipedia URL", "Genre_First_ID", "Genre_First", "Genre_Second_ID", "Genre_Second", "Director_First_ID", "Director", "Cast_First_ID", "Actor_First", "Cast_Second_ID", "Actor_Second", "Cast_Third_ID", "Actor", "Cast_Fourth_ID", "Cast_Fifth_ID", "Budget ($)", "Box Office Revenue ($)"}),
  #"Renamed columns 4" = Table.RenameColumns(#"Reordered columns 5", {{"Actor", "Actor_Third"}}),
  #"Merged queries 6" = Table.NestedJoin(#"Renamed columns 4", {"Cast_Fourth_ID"}, Actors, {"ID"}, "Actors", JoinKind.LeftOuter),
  #"Expanded Actors 3" = Table.ExpandTableColumn(#"Merged queries 6", "Actors", {"Actor"}, {"Actor"}),
  #"Reordered columns 6" = Table.ReorderColumns(#"Expanded Actors 3", {"Movie Title", "Release Date", "Wikipedia URL", "Genre_First_ID", "Genre_First", "Genre_Second_ID", "Genre_Second", "Director_First_ID", "Director", "Cast_First_ID", "Actor_First", "Cast_Second_ID", "Actor_Second", "Cast_Third_ID", "Actor_Third", "Cast_Fourth_ID", "Actor", "Cast_Fifth_ID", "Budget ($)", "Box Office Revenue ($)"}),
  #"Renamed columns 5" = Table.RenameColumns(#"Reordered columns 6", {{"Actor", "Actor_Fourth"}}),
  #"Merged queries 7" = Table.NestedJoin(#"Renamed columns 5", {"Cast_Fifth_ID"}, Actors, {"ID"}, "Actors", JoinKind.LeftOuter),
  #"Expanded Actors 4" = Table.ExpandTableColumn(#"Merged queries 7", "Actors", {"Actor"}, {"Actor"}),
  #"Reordered columns 7" = Table.ReorderColumns(#"Expanded Actors 4", {"Movie Title", "Release Date", "Wikipedia URL", "Genre_First_ID", "Genre_First", "Genre_Second_ID", "Genre_Second", "Director_First_ID", "Director", "Cast_First_ID", "Actor_First", "Cast_Second_ID", "Actor_Second", "Cast_Third_ID", "Actor_Third", "Cast_Fourth_ID", "Actor_Fourth", "Cast_Fifth_ID", "Actor", "Budget ($)", "Box Office Revenue ($)"}),
  #"Renamed columns 6" = Table.RenameColumns(#"Reordered columns 7", {{"Actor", "Actor_Fifth"}}),
  #"Added custom" = Table.TransformColumnTypes(Table.AddColumn(#"Renamed columns 6", "ROI", each ([#"Box Office Revenue ($)"] - [#"Budget ($)"]) / [#"Budget ($)"]), {{"ROI", Percentage.Type}})
in
  #"Added custom"
```

### Main Insights:

- Even though **Action is the leading genre in terms of total revenue**, suggesting both strong popularity and high production volume, **Adventure movies generate, on average, 25% higher revenue per film that Action**. However, in 2016, their values are almost even.
  
<img width="574" height="448" alt="image" src="https://github.com/user-attachments/assets/cdabde98-4cc5-44ff-a183-d8522aaf8ecc" />

- **Horror movies often rank among the top genres by ROI across multiple years** (though this should be interpreted with caution, since box office revenue is most probably only one part of a film’s total revenue).
  
<img width="658" height="272" alt="image" src="https://github.com/user-attachments/assets/1f5c0ee6-38be-480d-a9ef-948825d7b931" />

  
- In general, **July is the most appealing month** based on Box Office Revenue, especially for Adventure, Action, Comedy genre. Horror movies generated best sales in June. *It is best to use the most recent year for each genre as the primary reference point as it represent recent trends. I would also recommend analyzing average movie revenue instead of total revenue, as total revenue can be sometimes heavily influenced by the number of movies in each genre.*

  <img width="1298" height="572" alt="image" src="https://github.com/user-attachments/assets/af9f4b2a-234f-4991-ac4e-6c17f777f857" />

  
- Some actors may be popular and appear in many films. I would suggest evaluating a particular actor based on the average success of the films they appear in.
  
- <img width="498" height="430" alt="image" src="https://github.com/user-attachments/assets/063c3b8c-17ce-4bc0-ab4e-136ec8c21f33" />




