---
title: 'Indexes'
date: 2024-10-07T19:22:01-04:00
author: 'Kevin'
---

{{< hint type=note >}}
RootDB is currently in development and therefore this documentation may be a bit out of date for the actual algorithms and structures used, but for the most part will be up to date with the main branch of RootDB.
{{< /hint >}}

## What even are Indexes?

There are *many* articles and videos about what indexes are, some good, some bad. So I will not go into the science or practicalities of what indexes do or benefits, but for the purposes of this page all you need to know is that an index is just a data structure that lets databases find where a specific row is being stored in the database. For exmaple, you might have a million rows and each row has a User_ID. If you want to find where User_ID=500,000 is, the naive way is to go through each row to see if the User_ID matches the one we want and return that row. Obviously, this is not a good way to do this because we want to avoid having to look through rows as much as we can because there is a cost to looking at a row and with a million rows these cost add up. Indexes lets the databases take the specific value from a column we want to find and in a much more efficient way locate where it needs to look for this row and go to just that place to retrieve the row.

## What does RootDB use?

RootDB as of this writing uses B+Trees for its indexes. One of the best visualizations for what B+Trees do can be found on this [Website](https://www.cs.usfca.edu/~galles/visualization/BPlusTree.html).

A simple way of thinking what a B+Tree is: A binary tree but instead of 2 children it has N children.   
So why do we want N children? The most obvious answer is that it makes finding keys much faster because we have to jump through less nodes to find where the key is stored. Another great property is that in a B+Tree all the key-values are stored in the leaves and the leaves are all equal distance away from the root. Maintaining this balance increases complexitiy in code but the benefits are much greater than using Binary trees. B+Tree may be more complicated to implement but most popular databases use some variation of BTrees to implement their indexes because the benefits heavily outweight any negatives.

![Example of a B+Tree](../images/samplebptree.png "Example B+Tree")

## What does the B+Tree even do?

The one purpose of a B+Tree is to store in our case Key-Value pairs. What kind of Key-Value pairs? The key is the value in the column for whatever row we want to find it may be the ID row or it may be some other unique column value. The value is simply a number, this number happens to be a number that our database understands how to use to locate the physical location of where this row is stored. In order for our database to retrieve rows it has to read the actual data file and parse these rows and then compare the column to the one we want. As you can see this is a O(n) type search. B+Tree lets us skip reading the entire file and simply jump to the point in the file where this row is stored and read that row value to return to the user. 

The time complexity for searching, inserting, and deleting values from a B+Tree is O(logn). This is quite good and allows us to more efficiently query rows or ensure that a value is actually unique in a table.

For RootDB the current implementation of the B+Tree is Int64:Int64 Key-Value pairs. Since all the actual data is stored within the leaf nodes of the B+Tree we can then simply store the leaves in a file to make this tree persistent. A simple way to do this would simply be convert each node into some bytes and write this to the file, and that's exactly how RootDB does it. Every database write in RootDB is of size 4096, this seems like an arbitrary number but it's simply a nice number to work with when writing and reading from files if you want to know more about this number you can head to the File Format page to understand this. Therefore, our B+Tree is simply a list of leaf nodes stored in a file that is read on startup and creates the B+Tree to use. 

## Some interesting properties of B+Trees

We know that all the important information is stored in the leaves. These leaves are then connected as a singly linked list so starting from the first leaf all the way to end you can retrieve every single location for each row in the database as linked from the key. These Key-Value pairs are sorted in each node to make accessing them faster in the nodes, but the leaves are also sorted because this is a recursive structure and if one node is sorted all the nodes must be sorted. Therefore, every Key is sorted and this lets us do some fancy operations like retrieving all rows less than a certain Key or maybe all the ones greater and we can do this because we can just walk through the leaf nodes collecting all the values that satisfy our Key comparison.

It isn't all upsides though, having a B+Tree introduces some complexity into the code and requires every transaction to update the index if it is impacted. That's why they always say to only index the necessary columns because each index requires its own overhead and incurs a cost to maintaining this data structure.

## Looking forward

B+Tree will remain the main way of maintaining indexes and small changes to how the data is stored might occur but the general goal of B+Tree make them too worthwile to switch to another structure such as a skip list. That isn't to say there may be other structures introduced that compete with the B+Tree but these will mainly be supplemental for very specific purposes that the B+Tree may not be as efficient with.