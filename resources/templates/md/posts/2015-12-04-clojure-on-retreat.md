{:title "How Clojure should be evangelized?"
 :layout :post
 :tags  ["Functional programming" "Clojure"]
 :toc true
}

# Intro

Thankful PSI and JUG Poznan we had 6th time pleasure to participate in Code Retreat. What is exactly?
 - 5 iteration of pair programming + retrospectives
 - Always solving Conway's Game of Life
 - Deleting Code after each iteration
 - And obviously - TDD
 
 This year I decided to use clojure. What is quite interesting - there was a lot of people interested in 
 learning Clojure and after a while anybody who joined me was able to correct my silly mistakes =)
 
 After several iteration we come to the solution very similar to Christophe Grand's version (http://clj-me.cgrand.net/2011/08/19/conways-game-of-life/):
 
 ```
 (defn neighbours [[x y]]
   (for [dx [-1 0 1] dy [-1 0 1] :when (if (= 0 dx dy))] 
     [(+ dx x) (+ dy y)]))
 
 (defn next-board [board]
   (for [[point n] (frequencies (mapcat neighbours board))
              :when (or (= n 3) (and (= n 2) (board point)))]
          point)) 
 ```