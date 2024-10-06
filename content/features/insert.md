+++
title = 'Insert'
date = 2024-10-05T11:43:06-04:00
author = 'Kevin'
+++

### Syntax

> INSERT INTO *table_name* (*column1*, *column2*, *column3*, ...)  
> VALUES (*value1*, *value2*, *value3*, ...);

### Examples

```SQL
INSERT INTO Users (userID, name, email, age, isMale)
VALUES (1, 'Ricky Mendoza', 'testing@email.com', 23, true);
```
 - must specify columns currently
 - values must be placed directly in sql string currently (will be changed to avoid sql injection in future)
 - same constraints for table/column name applies here
 - string literals use *'*, number literals can be integer or floats, true/false are reserved keywords for bool literals