---
title: Code as data -  data as code 
tags: clojure 
---

I have been doing Lisp, Clojure and Scheme for years, but I have always found the topic of macros a little annoying. In my code I seldom use macros and honestly I think many of the canonical examples, like the definition of unless-function which is the inverte of the an if-test, as pretty overrated. I can create an unless-function in Haskell/Clojure in plain code using lambda functions and the time saved using a macro is only a matter of a good/bad inlining compiler. It is even possible to write in modern Java/C#, even though the code would not look very pretty.

But we ran into a real usecase when we were working on an upgrade of our software for [smarter search](https://tellusr.com). The basis our problem was that the solr-code had changed the arity of a constructor to a class - it needed one more parameter in versjon 8.8 than in 8.4.

Our software runs on top of Solr and we would off course like to support both the new and the old version. The solution for this in Java/Kotlin has always been to make tags/branches in git. This is however a hassle as every change will have to be updated in all older tags, as well as master. This means lots of potential trouble and even though the ifdef-directive in C/C++ is much criticized it really gives meaning to use in cases like this.

I was thinking about how to solve this in Clojure and pretty quick came up with the use of macros as best solution. In the code below I check in **compile time** how many parameters the java-constructor needs and then define different macros depending on the included version of Solr. With only a couple of lines I can maintain two breaking versions of Solr in one version and we will not have to distribute different jar-files for different versions of Solr. As macros operates in compile time we do not have any speed loss, like we would if we had tried to solve the problem with runtime reflections (which could be the Java-solution here)

Code example is below, enjoy :)

```clojure

(defmacro create-doc-slice [a b c d e f]
  (concat
    (list 'DocSlice. a b c d e f)
    (if (= 7 (.getParameterCount
          (first (.getDeclaredConstructors
                   (Class/forName "org.apache.solr.search.DocSlice")))))
      (list 'org.apache.lucene.search.TotalHits$Relation/EQUAL_TO)
      '()
      )))

; after this point it is possible to use the create-doc-slice macro.

```


