---
title: 'Parser'
date: 2024-10-07T19:19:16-04:00
author: 'Kevin'
---

{{< hint type=note >}}
RootDB is currently in development and therefore this documentation may be a bit out of date for the actual algorithms and structures used, but for the most part will be up to date with the main branch of RootDB.
{{< /hint >}}

## What is the Parser for?

Every major relational database has their own variation of SQL, mine is no different. At the current moment it is compatible with MYSQL syntax for SQL and is trying to closely follow Ansi SQL syntax. In simple terms, the Parser is meant to take a SQL query from the user and translate this into a structure that the database engine understands and can do work on to return/do what the user asked for.

This parser is a [LL(1) Parser](https://en.wikipedia.org/wiki/LL_parser). What this means is that the syntax for this SQL can be represented with a jump table and that's exactly what I do. It is simple and makes the code easily extendible and will remain as the main data structure for the parser. Below is the image for transitions for the current SQL implementation, Although all properties may not be fully supported by the database at the moment.

![Parser flow chart](../images/parserFlowChartLight.jpg "flow chart")

## Components of the Parser

The 3 main components in this Parser and generally for any programming language is the Lexer, Parser, and AST.

The lexer simply takes in the input query string and tokenizes this. It scans for keywords in the language or for literals passed by the users and returns the tokens that the parser understands.

The parser takes the tokens from the lexer and goes through them not only checking to see if it is the correct order of tokens in the language but also constructing the AST along the way. The user may incorrectly write the query so that they messed up the sequence of writing the query or some other error and the parser is responsible for catching these errors and returning the proper messafe for the user to understand where the query is wrong.

The AST is a structure that the database engine understands and can use to perform the necessary actions. This is what the parser returns to the engine, the engine then takes this AST and traverses through it and this traversal is what actually makes the database change or return some data. The AST can contain lots of information such as the table the user is looking to get/put data from/into. It can also be further adapted to be enriched with more information such as checking if the columns are indexed and therefore different strategies are used to perform the actions the user wants.

Most programming languages have these 3 structures in some form or another. They may not use the same strategies to parse a string or even use an LL(k) parser since there is many differnent types each with their own tradeoff. But at the end of the day these are the fundamental blocks to building a language and being able to perform certain actions from this language.