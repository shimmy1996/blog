+++
title = "The Quest to Hash Table"
draft = false
date = 2014-07-11
slug = "the-quest-to-hash-table"
+++

Several mistakes I made when I tried to write a hash program using C.

1.  A pointer that points to a pointer that points to a pointer that points to a pointer that points to a...

    Generating an array of pointers is the core step when creating a hash table, but the malloc function and the pointer part can be a little bit confusing. There are, however, general rules for this:

    -   How many `*` s we put before a variable tells us how many dereferences we need to get the original type we want. If we want to make an array of `*node` (or some other pointer/type of variables), then we should add one extra `*` when declaring the array: `node **node`;
    -   General formula for using malloc to collect an array containing `NSLOT` pointers to node: `array_of_pointers = (node **)malloc(NSLOTS * sizeof(node *))`. The rule still holds if we add more `*` to each node or subtract `*` simultaneously: `array_of_pointers = (node ***)malloc(NSLOTS * sizeof(node **))`.

    Do note that `array_of_pointers = (node **)malloc(NSLOTS * sizeof(node *))` is equivalent to `node **array_of_pointers = malloc(NSLOTS * sizeof(node *))`.  Don't be afraid to use index on objects created this way: it's fine...

2.  Be mindful when you add node to one of the linked lists, more so when you are freeing it. Do note that, when you are checking whether a key is already present in the hash table, if a the key already exists in the hash table, you will have an extra copy of the key. Make sure it is freed. Also do not forget to free the key afterwards.

Apologies if this seemed unfinished XDD.

I started writing this stuff five months ago and just did not got a chance to finish it till now... Tried to pull off some points from the original code I had, but this is pretty much all I got now XDD.
