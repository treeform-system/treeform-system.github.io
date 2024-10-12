+++
title = 'Bufferpool'
date = 2024-10-07T19:19:25-04:00
author = 'Kevin'
+++

{{< hint type=note >}}
RootDB is currently in development and therefore this documentation may be a bit out of date for the actual algorithms and structures used, but for the most part will be up to date with the main branch of RootDB.
{{< /hint >}}

## What is a bufferpool?

A bufferpool is just another fancy data structure that lets you reuse blocks of memory. Why is this important? because reading from a file is expensive. It takes longer to read from your Hard Drive than it does to read from your RAM. So for example, let's say you have a lot of books at home and you go away to college, you can only take so many books so you take the ones you really like and will read often and leave the less read ones at home. But sometimes you really wanna read a certain book from home, so when you go home you bring back some books you don't want to read anymore and bring back to college the ones you do so you have a new set of books at college. Going back home from college isn't easy so you can't exactly do this all the time and you want to minimize the number of times you are replacing books from home and that's what a bufferpool does it minimizes the number of times you have to retrieve a certain part of memory from a file or from some expensive place to read from.

## How does RootDB bufferpool work?

We store the data in files and when we want to read the rows to return to the user we have to read the file and we specifically read a *Page* (4096 bytes) if you don't know what I mean by *Page* go read the file format site. The bufferpool will then store the *Page* we just read in memory in case the user requests it again we don't even need to read the file we just read from RAM and end up working faster. We can only store so many *Pages* in memory though since databases are meant to handle data larger than memory there is a limit to how many *Pages* we can store in memory. So what happens when we reach the limit?

We have to overwrite one of the old *Pages* and rewrite it with the new *Page* we got from the file. But how do we decide which *Page* to overwrite? That is where Page Eviction Policies come in. All they do is give an algorithm to decide which *Page* to kick out and hopefully we pick  one the user won't request next. There is many different Eviction Policies and they each have upsides and downsides [GeeksForGeeks](https://www.geeksforgeeks.org/page-replacement-algorithms-in-operating-systems/) has a good article about some of them. In RootDB we use a LRU since this structure can be made with atomic functions and all that means it is thread-safe to use.

## Thread-safety

Every database has to be thread-safe because this maximizes the amount of work it can do and for any language calling this database it does not have to worry about data races from its own threads. While some languages cannot do multiple threads, golang is a great language for concurrency and as such developers have to think about thread problems that may occur such as data races. The bufferpool is the most affected by this, since reading from a file is inherently asynchronous and as such is perfect for golang to handle in multiple threads trying to read from the bufferpool and/or writing to the database files. Having only one thread do the work of taking request and returning data would limit the database way too much so we want to maximize the number of threads that can work on the file while also not messing up the database. To do this we need locks. 

## What locks do we use?

In RootDB the two main locks are Mutex Locks and RWMutex Locks. A Mutex is simply a way to stop mutliple threads from accessing something at the same time. All it does is it ensures when you lock the Mutex that only 1 thread can do this and continue on with whatever function you're writing. A RWMutex on the otherhand has a bit more overhead because this allows multiple readers to lock this RWMutex and no writer can operate on it at the same time which allows multiple threads to read at once but the data can't be modified during this. A RWMutex only allows one writer at a time though, so when a thread wants to lock the RWMutex to modify the underlying data of whatever it is you're securing it will queue up the writer lock and once all the readers let go of the lock it will stop any future readers from being able to access the data until the writer releases its lock. This gives us some better performance on read heavy tasks that still need to write to the data but not as often.

The bufferpool is one of the main components responsible for ensuring whatever requests comes from the user on whatever thread, that the data returned is not going to randomly disappear or be modified from some other thread. In databases there is a concept called A.C.I.D. where the I stands for isolated. In this case no request can affect another request so it's important the bufferpool properly locks the data to ensure only one request can modify it at a time. This also fulfills the C in ACID, where C is consistency and this means any request will leave the database in a proper state that will not break any future request or corrupt data.