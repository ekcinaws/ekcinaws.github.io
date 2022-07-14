---
title: 'Create Input Files'
date: 2019-02-11T19:27:37+10:00
weight: 6
---

This section shows how to create a valid set of employee data and shop data input files for the relational database.

<!--more-->

# Overview

Acme pulls and transforms data from a pair of .csv input files to populate a table in the relational database. 

The .csv input file pairs consist of:
 - an employee data file (.csv)
 - a shop data file (.csv)

## Database Fields

The database table populates with the following fields and types of data:
| Field  | Type  |  
|---|---|
| ID  | INT  | 
| DATE | STRING  | 
| MANAGER | STRING |  
| OPEN | STRING |  
| CLOSE | STRING |  
| DURATION | INT |  
| REVENUE | DOUBLE |  
| CCY | STRING |  

## Data Transformation

Acme pulls data from each .csv file and populates the corresponding database field. 

The following table shows fields or properties of each .csv file and the corresponding field in the database. 

### Employee File
| .csv File | Database | Transformation | Description|
|---|---|---|---|
| id  | ID  | none  | employee ID # |
| name| MANAGER | none | retrieved via employee ID # in the shop file |
| joined_date | n/a | n/a | not used |
| active | n/a | load only if y | only loads data for active employees (if active = "Y")

### Shop File
| .csv File | Database | Transformation | Description|
|---|---|---|---|
| Id | ID  | None  | shop ID # |
| .csv filename  | DATE  | add hyphens DD-MM-YYYY | date|
| manager | MANAGER | INT (employee ID) to STRING (employee name) | name of manager via join to the person file |
| hours | OPEN | parse first time; if blank DURATION and REVENUE are set to zero and the CCY to N/A | Parses the opening time from shop hours|
| hours | CLOSE | parse second time; if blank DURATION and REVENUE are set to zero and the CCY to N/A | Parses the closing time from shop hours|
| hours | DURATION | calculate difference between start and end times, in minutes; if blank DURATION and REVENUE are set to zero and the CCY to N/A | Total duration of shop open hours, in minutes|
| sales | REVENUE| none | sales/revenue for the date |
| ccy | CCY | transform to ISO code | ISO code e.g. USD, GBP|

## Example
The following is an example of an employee .csv file and a shop .csv used to input data into a table.
### person.csv
```
id,name,joined_date,active
A111,chris,02-02-2008,Y
1116,paul,26-07-2016,Y
#1213,tina,01-23-2015,N
A1012,bill,10-05-1995,Y
```
### shop_20180511.csv
```
Id,manager,hours,sales,ccy
201,A111,07:30-20:45,2561.45,£
201,1116,8:00-22:00,2561.45,$
111,A1012,,,
1112,#1213,09:00-18:45,1000,£
1113,A1012,12:00-13:00,125.76,£
```
### Database Output

| ID | DATE | MANAGER | OPEN | CLOSE | DURATION | REVENUE | CCY |
|---|---|---|---|---|---|---|---|
|  201 | 2018-05-11 | paul  | 8:00 | 22:00 | 720 | 2561.45 | USD |
| 111 |  2018-05-11 |  chris | null  | null  | 0  | 0  | N/A  |
| 1113  | 2018-05-11  | bill  |  12:00 | 13:00  | 60  | 125.76  | GBP  |

### Notes
The first line in the .csv file labels the data of the subsequent lines, in order. The first line does not populate the database table.

Data for employee Tina (line 4 of person.csv) does not populate the database table because Tina is marked not active ("N").


