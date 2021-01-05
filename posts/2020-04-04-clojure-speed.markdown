---
title: Clojure, speed, benchmarks, large mutable datas structures and arrays
tags: cloure, arrays, speed, benchmarks
---

I love working with Clojure. The language is clean and clearly defined and when working in it one practically programs and defines the algorithms at the same time. 

The results pretty much just work and the code is very short and maintainable.

But sometimes, when it comes to algorithms, one can not avoid to work with large, mutable data structure.

In this case I was working with a large 2d array and while initiating the array I did pretty much write a straight forward implementation like this:

```clojure
(defn speed-test [s]
  (let [arr (make-array Double/TYPE s s)
        arr-list (range s)
        ]
    (doseq [i arr-list j arr-list]
      (aset-double arr i j 10)
      ) arr)
  ) 
```

Running the code took to my surprise about 15000 ms with a 1000x1000 array, even after improving the code a little with warn-on-reflection and warn-on-boxed.

I decided to do the same stuff in java to test:

```java

public class Main {

    public static void main(String[] args) {
	// write your code here
        int s = 5000;
        double[][] arr = new double[s][s];
        System.out.println("Starting");
        for (int i=0; i < s; i++) {
            for (int j=0; j < s; j++) {
                arr[i][j] = 88;
            }
        }
        System.out.println("Stopping");
    }
}

```

Same code took around 50 ms and was around 300x faster. I was going to work on much larger arrays that this test case. Oh, my.. 

I love the immutable data structures in clojure, but I also have learned the hard way to double check these when my stuff runs slowly.

So I pretty much reimplemented my first attempt without the immutable structures. I used recursion and trampolines to imitate the java loop as closely as possible.

```clojure
(defn speed-test2 [s]
  (let [arr (make-array Double/TYPE s s)
        i 0
        j 0]
    (letfn [(inc-and-write [i j]
              (cond (= s j) nil
                    (= s i) #(inc-and-write 0 (inc ^int j))
                    :else
                    (do 
                        (aset-double arr i j 10)
                        #(inc-and-write (inc ^int i) j)
                        )
                    )
              )]
      (trampoline inc-and-write 0 0))
    arr))
```

The program runs fine and there is no problems with stack traces while recursing, due to the trampoline.

But to my disappointment it was just a little faster than my first clojure attempt, and still far to slow.

After some profiling the large non-speed culprit turned out to be the aset-line in the inner loop. This was a little surprising, as it looks pretty innocent and well typed, as it even has double as a part of its name.

Taking a look at the clojure source defines this function like this:

 ```clojure
(defn aset
  "Sets the value at the index/indices. Works on Java arrays of
  reference types. Returns val."
  {:inline (fn [a i v] `(. clojure.lang.RT (aset ~a (int ~i) ~v)))
   :inline-arities #{3}
   :added "1.0"}
  ([array idx val]
   (. Array (set array idx val))
   val)
  ([array idx idx2 & idxv]
   (apply aset (aget array idx) idx2 idxv)))

(defmacro
  ^{:private true}
  def-aset [name method coerce]
    `(defn ~name
       {:arglists '([~'array ~'idx ~'val] [~'array ~'idx ~'idx2 & ~'idxv])}
       ([array# idx# val#]
        (. Array (~method array# idx# (~coerce val#)))
        val#)
       ([array# idx# idx2# & idxv#]
        (apply ~name (aget array# idx#) idx2# idxv#))))
 ```

From this I see that is actually using Array from java.lang.reflections - so there is reflections going on here.

I wrote a java helper, like this:

```java
    public static void setDoubleVal(double[][] arr, int x, int y, double val) {
        arr[x][y] = val;
    }
```
and then substituted the aset-double the function with my own version.

And the voila - my clojure code ran on par with the java version. One line and a 300x speedup.

## Conclusion

Using reflection might save some code lines as it gives generics for free, but in cases like this I *really* would prefer a specialized function, as the name indicates this already. 

Hopefully the functions like aget-double and is cousins will be fixed in future clojure releases, but in the mean time I will just have to keep it in mind.

