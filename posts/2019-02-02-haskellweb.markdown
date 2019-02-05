---
title: Trying to write some web stuff with Spock and Haskell 
tags: haskell, web, benchmarks
---

By far Haskell is the prettiest of the programming languages. If I am in doubt about how an algorithm works, I often just find an example in Haskell to get enlightened quickly.

Have a look at the canonical quick sort

``` haskell
 quicksort [] = []
 quicksort (x:xs) = quicksort small ++ (x : quicksort large)
   where small = [y | y <- xs, y <= x]
         large = [y | y <- xs, y > x]
```

or merge sort, borrowed from [here](https://www.csc.depauw.edu/~bhoward/courses/0203Spring/csc122/haskintro/)

``` haskell
merge [] ys = ys
merge xs [] = xs
merge (x:xs) (y:ys) = if x <= y
                      then x : merge xs (y:ys)
                      else y : merge (x:xs) ys

mergesort [] = []
mergesort [x] = [x]
mergesort xs = let (as, bs) = splitAt (length xs `quot` 2) xs
               in merge (mergesort as) (mergesort bs
```

Can´t be done better, can it?

So far I have not done any web stuff with Haskell. It would be fun to try out some of the solutions that exists for this.

After a quick look I decided to take a look at Spock. The server looks simple enought as well as easy to build out. These two features are by the way always two of the most important factors I look for, when evaluating techlogies.

Okay, so I create a stack project and add Spock to the build-depends in my cabal-files. When doing a “stack build” I see that the latest stack version of Spock does not like the current version stm-containers.

So I decide to learn a little more about how to use stackage.

I checkout version 1.1.02 of stm-containers and build - and a stack install.

I checkout latest version of Spock and Spock-core and bulids.

In my own stack project I have to add these two lines in the packages section:

#### packages: 
- /home/petter/dev/haskell/Spock/Spock 
- /home/petter/dev/haskell/Spock/Spock-core

In the extra-deps:

#### extra-deps: 
- reroute-0.5.0.0 
- stm-containers-1.1.0.2 
- stm-hamt-1.2.0.2 
- primitive-extras-0.7.1

And then my stack project uses my local repos - and (hey!) it works.

I am now able to run my project.

Creating hallo world with Spock is now as easy as writing this Main.hs

``` haskell
{-# LANGUAGE OverloadedStrings #-}
module Main where

-- import Lib
import Web.Spock
import Web.Spock.Config

type Server a = SpockM () () () a

app :: Server()
app = get root (text "Hello world!")

main :: IO ()
main = do
  cfg <- defaultSpockCfg () PCNoDatabase ()
  runSpock 8080 (spock cfg app)
```

Okay, now running it with

stack exec testspock-exe

and it answers fine on port http://localhost:8080

Okay, now I am curious to do some benchmaking. I use [wrk](https://github.com/wg/wrk)

wrk http://localhost:8080

Results are like this:



``` haskell
Running 10s test @ http://localhost:8080
  2 threads and 10 connections
  Thread Stats   Avg      Stdev     Max   +/- Stdev
    Latency     1.56ms    3.08ms  45.97ms   93.21%
    Req/Sec     6.34k     1.10k    8.51k    72.50%
  126347 requests in 10.01s, 42.78MB read
Requests/sec:  12626.58
Transfer/sec:      4.27MB

```

This are very nice numbers, as I am running on an old (but nice) Linux box.

One thing, however. Several minutes after running the benchmark the Spock sever still use 16% of the resurces in while doing a top.

I do a new an more aggressive benchmark

wrk -t 10 -c 50 http://localhost:8080

The results are a little lower, but cpu now seems stuck at 30% usage even hours after the benchmarks and when the server has nothing to do.

I am not sure what happends here. Could be a regression as I work on the bleeding edge of the git, or something that I have not configured right?

But for now I think I will test another framework.


