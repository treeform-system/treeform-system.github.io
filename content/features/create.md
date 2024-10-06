+++
title = 'Create'
date = 2024-10-05T11:43:27-04:00
author = 'Kevin'
+++


### Syntax

> CREATE TABLE *table_name* (  
>&nbsp;&nbsp;&nbsp;&nbsp;*column1* datatype constraint...,  
>&nbsp;&nbsp;&nbsp;&nbsp;*column2* datatype constraint,  
>&nbsp;&nbsp;&nbsp;&nbsp;*column3* datatype constraint,  
> ....);

### Examples

```SQL
CREATE TABLE Users (
    userID INT PRiMARY KEY,
    name CHAR(32) NOT NULL,
    email CHAR(32) NULL,
    age FLOAT,
    isMale BOOL
);
```

 - currently supported datatypes are INT, FLOAT, BOOL, CHAR. Where size of CHAR column must be specified (ie. column CHAR(number) constraint...)
 - same constraints for table/column name apply as above
 - total row size for all columns added together may not exceed 4070 bytes in total
 - CHAR field will error when trying to insert strings larger than specified but will allow strings lower in size
 - current constraints are nullable or not nullable and primary key
 - may only have one primary key and must be integer field