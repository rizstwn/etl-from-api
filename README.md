# Simple ETL simulation from API
Simulating simple ETL (Extract, Transform, Load) process using data retrieved from API as a project for Midterm Exam of Data Engineering course

## API Used
The [Rick and Morty API](https://rickandmortyapi.com/) is a REST(ish) and GraphQL API based on the television show Rick and Morty. You will have access to about hundreds of characters, images, locations and episodes. The Rick and Morty API is filled with canonical information as seen on the TV show.

The API will automatically paginate the responses and each page consist of 20 documents.

## Workflow
### Logging
Logging is done in a simple text file in a format of **[Time of Activity] Description of Activity**. For each step in the Workflow, logging is done to describe what activity that is being executed and what time it is executed.

### Retrieve Data
Data retrieved by download each file from the API. 3 files were downloaded:

1. `Character`: Characters that have appeared in the Rick and Morty tv show
2. `Location`: Locations that have been shown in the Rick and Morty tv show
3. `Episode`: Episodes of Rick and Morty tv show that have been aired

Downloaded files only contain 20 data so extraction from each page is needed to get all of the data.

### Extract Data
`info` in the downloaded files contain info about page count and url to the next page. Looping for each page is done based on page count and next page url. All data received in this manner is saved in an array called `total_data`. Final data is then saved in a json and also converted into a DataFrame

### Transform Data
There are some steps done to transform the data to prepare them for insertion in a single database that consist of 3 tables connected to each other. The schema of the database can be seen in the IPYNB file. The transformation steps are:

1. `Delete column`: Columns that are irrelevant for the database such as `url` and `created` are dropped
2. `Replace missing value`: Some values in the data only consist of `''` or NULL value. For the sake of uniformity, all missing value are replaced to `np.nan`
3. `Change column name`: Some column names such as `origin` and `episode` does not quite represent the values. These column names are then changed to a more fitting names
4. `Change data type`: Some values such as in `origin` are consist of multidimensional data such as dictionary. For the sake of consistency before loading to dataset, This multidimensional data are changed to another datatype while maintaining the purpose of that data.
5. 'Adding new column`: Based on the database schema, Some DataFrames need to be combined. To make it easier, new column is added with `np.nan` values
6. `Combine DataFrames`: Before loading the data into the database, `character` and `location` DataFrame are combined based on database schema.
7. `Change date format`: The original date format may not be supported in some programming language so it is changed into a more common format
8. `Make Fact table`: A table in the database may have many-to-many relationship with another table. In that case, new table called `fact table` that consist of primary key of both tables are made.
9. `Remove list values`: Some values that are of list data type are drop from the DataFrame.
10. `Change "Unknown" to np.nan`: Some `unknown` values are converted to `np.nan` since the value of them are unknown and for the sake of uniformity

### Load Data
Database used in this phase is SQLite3. There are 4 steps in this phase:

1. Making a connection to the database
2. Making tables based on the database schema
3. Inserting data from DataFrames to each table
4. Close connection to the database

### Query Data
To test if the process worked and to find some information from the data, some SQL queries are done. The queries executed are:

1. Insert Query
2. Update Query
3. Delete Query
4. Select Query
5. Aggregation Query
