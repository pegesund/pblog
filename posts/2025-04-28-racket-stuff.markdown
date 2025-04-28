---
title: Updating scylladb-driver for Racket, Scheme - and reading some poetry 
tags: racket, scheme, scylladb
---

I decided to take a look at Racket scheme as it has been a project over long time, improving all the way. I also like the Scheme syntax better than Lisp one, it is more exact and less bloated. I tried out the Cassandra driver for Racket, but for my version of Scylla, the protocols did not match. Scylla had moved on and left the old protocol behind and now only supported the newer, fully async version.

These days many use llm's for generating code, off course I do to, in some cases. But this comes with an overhead - words become less valuable. They are so easily available and therefore spendable. In the future words and code will be produced by llm's as well as conumed by llm's. This struck me while I was reading the greek book Iliad and after this digging into Alice Aswalds book [Memorial](https://www.amazon.com/Memorial-Alice-Oswald/dp/0571274188) which is a poem dedicated to all the mytic figures that lived and died in Homers epos:

The first to die was PROTESILAUS  
A focused man who hurried to darkness  
With forty black ships leaving the land behind  
Men sailed with him from those flower-lit cliffs  
Where the grass gives growth to everything  
Pyrasus Iton Pteleus Antron  
He died in mid-air jumping to be first ashore  
There was his house half-built  
His wife rushed out clawing her face  
Podarcus his altogether less impressive brother  
Took over command but that was long ago  
He’s been in the black earth now for thousands of years  



Oh, does it exist a better start on a book? 

Maybe not. Not one word to spoil! What strikes me here is that every word is valuable. When I looked at the old code for the Cassandra (!) driver in Racket, I understood better why I always have liked Lisp/Scheme. There is no bloated overhead, no factories, interfaces, injections and large frameworks. Every line and word counts in this beautiful functional language, and even though Lisp is both untyped and claimed to be hard to read due to the parentheses I find my way in the code in seconds. It is simply because every code here is valuable, everything counts.

After some hours I had implmented the protocol. Code still looks like the old one, but I had to change some of the main structures due to the new async nature. And it is really fast, over 20k reads pr second without a hickup. A really nice experience, and also a very helpfull and community on Discord. Have a look and find the real value of coding :)

Yes, code is [here](https://github.com/pegesund/racket_scylladb)
