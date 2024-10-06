+++
title = 'Getting Started'
date = 2024-10-05T11:43:12-04:00
author = 'Kevin'
+++

### Install

pacakge can be installed with go get (go 1.22 or higher recommended):
```plain
go get github.com/treeform-system/rootdb
```
### Features

Currently the following features are supported

 - SELECT
 - INSERT
 - CREATE

### Examples

A simple usage would be:

```golang
package main

import (
	"database/sql"
	"fmt"
	"log"

	_ "github.com/treeform-system/rootdb"
)

func main() {
	fmt.Printf("Drivers=%#v\n", sql.Drivers())

	db, err := sql.Open("rootdb", "example")
	if err != nil {
		log.Fatal(err)
	}
	defer db.Close()

	err = db.Ping()
	if err != nil {
		log.Fatal(err)
	}

	_, err = db.Query("CREATE TABLE MyTable10 (column1 int Primary Key, name char(10),column30 bool, column400 float, column5 int);")
	if err != nil {
		log.Fatal(err)
	}

	_, err = db.Query(`INSERT INTO "MyTable10" (column1,name,column30,column400,column5) VALUES
	 (1,'somecharss', true,1.23,2),
	 (2,'10letters', false,4.69,2),
	 (3,'Kevin', true,'.567',2),
	 (4,'tim', false,.678,2),
	 (5,'stringsss', true,5.36,3);`)
	if err != nil {
		log.Fatal(err)
	}

	rows, err := db.Query("SELECT * FROM MyTable10;")
	if err != nil {
		log.Fatal(err)
	}
	fmt.Println("Select Query passed")

	for rows.Next() {
		var i int
		var n string
		var b bool
		var f float64
		err := rows.Scan(&i, &n, &b, &f)
		if err != nil {
			log.Fatal(err)
		}
		fmt.Printf("(%d) (%s) (%t) (%f)\n", i, n, b, f)
	}

	rows, err = db.Query("SELECT * FROM MyTable10 WHERE column30 = true AND column1 > 1 AND column5 > 2;")
	if err != nil {
		log.Fatal(err)
	}
	for rows.Next() {
		var i int
		var n string
		var b bool
		var f float64
		var ii int
		err := rows.Scan(&i, &n, &b, &f, &ii)
		if err != nil {
			log.Fatal(err)
		}
		fmt.Printf("(%d) (%s) (%t) (%f) (%d)\n", i, n, b, f, ii)
	}
}
```