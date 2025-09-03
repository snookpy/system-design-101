---
sidebar_position: 3
---

# Indexes
Indexes are well known when it comes to databases. Sooner or later there comes a time when database performance is no longer satisfactory. One of the very first things you should turn to when that happens is database indexing.
An index is a data structure that can be perceived as a table of contents that points us to the location where actual data lives.

## Purpose of Indexes
- **Faster Data Retrieval:** Indexes significantly speed up query execution by providing a more efficient means of locating data, which can lead to a reduction in the number of disk I/O operations and CPU usage.

- **Sorting and Ordering:** Indexes can be used to quickly sort and order the data in a table based on specific criteria, which can be useful for reporting or displaying data in a specific order.

## Indexes Improve Query Performance
- **Reduced Table Scans:** By using an index, the database can avoid full table scans, which require reading every row in a table. Instead, the database can directly access the indexed columns, reducing the amount of data that needs to be read and processed.

- **Efficient Data Access:** Indexes provide a more efficient means of accessing data by organizing it in a way that allows the database to quickly locate the rows that meet the query criteria.

- **Index Selectivity:** Indexes with high selectivity can improve query performance by reducing the number of rows that need to be accessed. High selectivity means that the index can effectively filter out a large number of rows, thereby reducing the amount of work required to process a query.

## Indexes decrease write performance
While indexes can significantly improve query performance, they also come with some overhead.
Indexes require additional storage space and can slow down write operations, such as INSERT, UPDATE, and DELETE, since the indexes must be updated along with the table data.
When adding rows or making updates to existing rows for a table with an active index, we not only have to write the data but also have to update the index.

## Types of Indexes
There are various types of database indexes, each with its unique characteristics and use cases.

### Primary Index
Primary Index is the index on a table’s primary key. The primary key is a column (or set of columns) that uniquely identifies each row. Most relational databases automatically create an index on the primary key to enforce uniqueness and allow fast lookup by the primary key.

There can only be one primary index per table because a table has only one primary key.

Improves read performance for identifying specific rows Like;
```sql
SELECT * FROM Employees WHERE EmployeeID = 123
```

