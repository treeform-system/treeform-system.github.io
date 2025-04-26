+++
title = 'Roadmap'
date = 2025-04-25T20:21:08-04:00
draft = false
author = 'Kevin'
+++

## Next Steps

- WAL manager
    - push to back process and have data move from WAL to data files over time and balance pressure from heavy writes.
- Join operator
- B+ Tree optimization
- Driver bindings for other languages

## Future Products

### ***MockMint*** or   ***SeedlingSim***

similar to [Mockaroo](https://www.mockaroo.com/) but having it directly generate the database files instead of the user having to convert/parse them into some test database.
Purpose is to make mocking test faster instead of having to setup another test container/database

### ***SQLSeed***

similar to [SQLC](https://docs.sqlc.dev/en/latest/index.html) but specifically for this database engine so it can take advantage of our built-in functions as well as implementing this for multiple languages not just Go.
This makes creating the boilerplate code for the common sql query very easy to setup and not have to write all the boilerplate associated with these common patterns. While still giving a lot of flexibility to what you want the end code to interface with.

### ***RootDB viewer***

similar to [SQLite browser](https://sqlitebrowser.org/) but for this database and possibly in the terminal using [Charm](https://charm.sh/) or keeping it as a gui.