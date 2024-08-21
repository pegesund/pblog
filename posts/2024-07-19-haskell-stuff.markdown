---
title: Doing Rusty Stuff
tags: rust
---

## Rust, the New Kid on the Block

In programming, there's no doubt that there's a new kid on the block. Or well, not so new... Rust has very quickly become a go-to solution for new projects that need speed and good control over memory usage. Much has been said about the advantages of the way Rust deals with memory, and I really felt I had to give it a try.

By the way, it has been a while since my last post; my only excuse is too much work.

## First Impression

While starting, I very quickly ran into the same frustrations as most new Rustaceans do, it seems. Understanding the fundamentals of the borrowing system takes some hours, and then after a few days, I learned some more the hard way as I had to restructure my code pretty much.

But after some time, most things started to work okay, and I really like much about this language. Readability is very good, and the type system is very precise and well-built. While using it, I was really glad that I had been programming in both Haskell and C before. This gives a better understanding of the language and why it is designed as it is. The type system catches many errors at compile time but is not as well developed as the one in Haskell, for example. If you would like a library to cover different types of numbers, like 32-bit float and 64-bit float, this is often solved with macros (by the way, Rust has hygienic macros, just like in Scheme!). But the language has good support for other nice features like the mentioned memory safety, async handling of I/O, and higher-order functions.

## Giving It a Try

### HNSW and Search

I like to try to make something useful while coding, and here I could combine work and interest in a pretty neat way. At work, I have been working with semantic search, and algorithms like HNSW have been pretty important here. In short, HNSW clusters semantically equal vectors so it is very fast to search for similar items.

Unfortunately, this functionality came late to the Solr/Elastic stack, so we experimented with a Rust solution for filling the gaps. I really liked the code in this project: https://github.com/jean-pierreBoth/hnswlib-rs, so I decided to use this as a starting point.

The first pull request here was the implementation of the Levenshtein distance as a possible metric. This makes it easy to search for misspelled words very fast - the HNSW is a kind of index of words that are spelled almost equally.

Next was the need for filters. Filters in HNSW are discussed in several places, and in the very nice Vespa solution, you can find some filter strategies and their advantages and costs explained [here](https://blog.vespa.ai/constrained-approximate-nearest-neighbor-search/#controlling-the-filtering-behavior-with-approximate-nearest-neighbor-search), for example.

The two strategies above explain two approaches which we also considered.

Prefiltering means first doing a search on the filter (like the category "sport" in a web shop, for example) and then collecting all possible IDs and doing an HNSW search with these IDs as params. The problem here is that this might take a long time if the number of IDs is big (there are many documents in this category).

Postfiltering avoids this problem by searching in all content and then picking out results corresponding to the filter after the initial search. This comes with another problem - you cannot be sure that there are enough (or any at all) of the correct document type in the result set. So if one asks for the top 10 best hits in a category, you might get none.

So I settled on another strategy which is more like in-filtering. It works by iterating the HNSW graph in a normal way, but hits are not accumulated before being in the correct filter. This avoids most of the pitfalls in the two strategies described above, but I was afraid it would be complex to implement.

However, as the codebase is really a beauty and as Rust has very nice support for anonymous functions called closures, this turned out to be pretty straightforward. I found the point where the result was accumulated in the algorithm and then just added a couple of lines calling a Rust closure. If the closure returned true, the result was added to the return list; otherwise, it was not. The closure was added as a parameter while creating the HNSW, as shown in the example file [here](https://github.com/jean-pierreBoth/hnswlib-rs/blob/master/tests/filtertest.rs).

So what does it mean that a closure returns true? In a closure, you can simply add the logic you would like in plain Rust, for example, checking if the document was in the correct filter collection - which also means that it is possible to do in-time filtering while iterating the HNSW.

### Looking at RocksDB

I often work with big data and need a really fast solution to store data on disk. RocksDB is one of the best in this area, and large commercial cool actors like YugabyteDB and CockroachDB are built directly upon this solution.

I decided to take a look at the Rust wrapper and quickly ran into the problem that it was not possible to add sort order to the indexes without doing lots of raw byte encoding and decoding. I went for the same strategy as described above and created a pull request where sorting of items in a RocksDB index can be ruled out by a closure, as described [here](https://github.com/rust-rocksdb/rust-rocksdb/pull/765).

I also created a library for making it easy to deal with complex sort orders and where one can, just like in SQL, sort stuff by ascending/descending and in combined ways, to be found [here](https://github.com/pegesund/sortable_rocksdb_keys). Benchmarks are always complex, but some testing on my laptop shows writing millions of rows pr. second is within reach, so this is pretty promising I think. Plan is to use this library in a larger project, but more about this later. Any usage or testing in the mean time would be more than welcome!

## Back to Rust

So I write this to sum up my experience with Rust. I really found this:

- Rust is really robust. After getting the code to compile, it surprisingly often works. But more advanced memory models like shared read/write between threads have their challenges, and it is absolutely not impossible to leak memory or crash the system in this language as well.
- Pattern matching makes code very readable.
- Of course, it is fast and memory-safe.
- I like the concept of traits, types, and functions.
- Not so happy about the macros. I would have preferred a more built-out type system and less use of macros. It makes debugging harder, as you get references to a line in the macro, not in your code.
- Rust and the package system are very fast and nice. Cargo did not fail me at all, and having had my share of trouble with other systems like pip and Maven, this is a big win.
- Error handling is complex in most low-level languages, but I like the concept of catching result types.
- The community is really nice and welcoming. There are lots of Rust projects which really welcome new contributors in a nice way, it seems.
- Compared to my C and C++ code, Rust code is so readable.

So would I use it? For speed tasks, yes - for other stuff like web solutions, there are coming solutions for this, and some libraries for compiling WebAssembly look interesting, but for most other stuff, the short answer is, of course, no. Rust is mostly for high-speed and low-level stuff. But it's very interesting and also good fun to write in.
