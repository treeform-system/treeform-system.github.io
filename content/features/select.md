+++
title = 'Select'
date = 2024-10-05T11:42:34-04:00
author = 'Kevin'
+++

### Syntax

> SELECT *column1, column2, ....*  
> FROM *tableName*  
> WHERE *condition*;

### Examples
```SQL
SELECT * FROM Users;
```

 - optionally instead of listing columns can use * to select all columns from table
 - where condition accepts first parameter as column identifier and second parameter may be column identifer or string/number literal
 - where field not necessary
 - column and table name identifiers only accept strings that contain ASCII characters (a-zA-Z) as first character and (_a-zA-Z0-9) for second character (you can use the regex [_a-zA-Z][_a-zA-Z0-9]* to test if your string works), *'* is reserved for string literals, to specify and identifer literal use *"* and any sequence of characters within will be valid
 - max length of 255 bytes for column/table name
