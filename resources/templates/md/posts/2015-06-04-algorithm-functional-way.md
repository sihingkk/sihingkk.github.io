{:title "Finding Minimal Edit Script functional way - part 1"
 :layout :post
 :tags  ["Functional programming" "Clojure"]
 :toc true
}

# Purpose

I got recently excellent assignment - to implement algorithm finding Minimal Edit Script. As usually it's a task that is
only interesting for person writing it, as the topic is really nicely defined and
most implementation (for example used in GIT) are based on papers written by
Eugene W. Myers in his papers [An O(ND) Difference Algorithm and its Variations](http://xmailserver.org/diff2.pdf).
We are going to implement that in functional way - and use it to compare how those different approaches differs in terms of code readability and performance.

Why Minimal Edit script is cool? Because it leads us directly to calculating [Levenstein Distance](http://www.levenshtein.net)

Why we want to implement it in functional way? Because I trully believe that this could be better:

![Minimal edit script](/img/ses_alg.png)

Please note that this is not script calculating Minimal Edit Script, only it's length. We will go a bit further and calculate an exact Minimal Edit Script.

# Tech stack

As we would like be as buzz-word oriented as it's possible, I'm going to write nice algorithm in ~~Scala~~ *Nope. Clojure. Please don't be afraid of brackets*.  But firstly we need to understand algorithm itself.

# Algorithm explanation

For sake of simplicity I will assume that elements of the list are letters. To explain solution we need firstly to introduce several concepts

## Minimal Edit Script

A minimal edit script is set of instructions transforming the former into the latter. 
For example:
For Kiteen And Sitting:
* **k**itten → **s**itten (substitution of "s" for "k")
* sitt **e** n → sitt **i** n (substitution of "i" for "e")
* sittin → sittin **g**  (insertion of "g" at the end).


## Editor table
is a matrix nxn where we have first world on one axis and second word on another. A path from top-left to right-bottom corner
can be interpreted as edit script, where:

* going down -> adding a letter
* going right -> removing a letter
* going cross when letter are equals -> leaving a letter 
* going cross when letter are not equals -> substitution a letter

for example:

Kitten
Sitting

# Functional Code

Functional Programming is usually about reducing problem to find a proper data strucure and finding set of transfomations.

In our case datastructure will be a vector [xs ys path] where 
* xs is first word
* ys is second word
* path is a minimal edit script translating xs into ys

The algorithm itself is about finding set of paths allowing 1 more change each step. So firstly we check what paths are 
when we allow one change, than how far we can go with 2 changes etc.
Hence finding a solution is extending every possible path so long as we get an bottom right corner:

```
(defn next-paths [envelopes]
    (union ((comp (move cross) (move right)) envelopes)
           ((comp (move cross) (move down)) envelopes)))

(defn- extract-path-for-solutions [[xs ys path]]
    (if (every? empty? [xs ys]) path []))

(defn find-path [envelopes]
    (mapcat extract-path-for-solutions (iterate next-paths envelopes)))
```

Moving in some direction in our case means removing item from one list, and adding info about where we moved:

``` (defn- move [where]
    (partial map where))
    
(defn- down [[xs ys path]]
    [xs (rest ys) (conj path :down)])
    
(defn- right [[xs ys path]]
    [(rest xs) ys (conj path :right)])   
```

