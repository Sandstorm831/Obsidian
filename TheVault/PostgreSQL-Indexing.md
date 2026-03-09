• `Indexes` are fundamentally a separate, discrete data structure
• On applying indexes on a particular table, it creates a separate data-structure which is optimised for particular operations
• Most common form of that data-structure is `B-trees`. `GiST` indexes we saw earlier is a different type of index structure
• indexes maintain a copy of a part of your data, when you create a index on a particular column it creates a copy of that column from table and puts them in the index in a way that makes look-ups easy and fast. This is a reason why you shouldn't create indexes for everything as it slows you database down as every-time we update/insert the data, the index need to be maintained (order in index can also need to be updated). It's a quick operation but it still takes some time.
• In `postgres` each index contains a pointer back to the table and the physical location of where that row is however in most databases every index contains a pointer to the primary key.
