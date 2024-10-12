+++
title = 'Wal'
date = 2024-10-07T19:19:32-04:00
author = 'Kevin'
+++

{{< hint type=note >}}
RootDB is currently in development and therefore this documentation may be a bit out of date for the actual algorithms and structures used, but for the most part will be up to date with the main branch of RootDB.
{{< /hint >}}

## What is a WAL?

Databases must be durable and fast, and one way they maintain this is by having a WAL. A Write Ahead Log (WAL) is a file that all transactions modifying data write to, and once they commit the transaction, this change eventually gets merged with the data files. The WAL is an append-only file where all transactions get saved to initially, and having this as a separate file allows any transaction looking to read data not to be blocked by writers, therefore improving the concurrency in the system.

## Why not just write to data files?

Well, sometimes a transaction does not commit, and if we write to the file, we have to retrieve the data, modify it, and then write the file, and this is expensive because at the very least we are making 2 IO operations on files, which are inherently slow. By having the WAL and writing all transactions even if they do not stay, we avoid having to unnecessarily write multiple times to a data file in case the user does not commit a transaction, and if their system crashes for some reason, all transaction operations are recorded. Computers can crash, and it is the responsibility of a database to be able to not get corrupted and retain all the information it was given. Even if the user only modifies one tiny column in one specific row, we have to rewrite the entire *Page* to save this operation. The WAL only has to record the transaction, which is significantly smaller.

## How does a WAL work?

A WAL is effectively a log file of all the transactions currently happening. So the entire database can effectively be implemented within a WAL file, but a WAL is not efficient when reading or actually storing the data; therefore, we want to merge our WAL file with our data files (which are effective for reading) every so often. There are different ways to decide when to do this, but for the purposes of this overview, it is unnecessary to know, and it's enough to know that it eventually gets merged so the WAL file never gets too big. The WAL contains all the transactions, even ones' that haven't been committed yet, because the WAL is a snapshot of the current transactions that have occurred at any moment, and the system can crash at any moment, but we have a way to restore the database to a proper state after reading the WAL so no transactions actually get lost even if the system crashes. The WAL keeps growing with every transaction, and when we merge the WAL back to the data files, this is much more efficient since we can do it for lots of data at a time instead of every single unitary transaction.

## Downsides?

As you can see, the WAL is important in not only making the database durable in case of a system crash but also increasing the throughput of the overall database system by allowing multiple readers independent of writers and having writes happen much faster. But it is not all upsides. The WAL introduces some complexity into the system that the database is responsible for maintaining. We also have to read the WAL file on startup each time in order to restore the database to a proper state, which incurs a time cost on startup. The WAL must also be kept in a separate append-only file from our data files, making the size of the database slightly larger. Whenever the WAL is merged back to the data files, this comes at a cost because we are writing multiple transactions into our data files and it blocks all the writers from being able to write new transactions until this merge is finished.