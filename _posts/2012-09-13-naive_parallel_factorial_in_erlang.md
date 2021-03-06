---
layout: post
title: "Naive Parallel Factorial in Erlang"
date: Thursday, September 13 2012 21:05 PM
comments: true
---

I have been toying around with [Erlang](http://www.erlang.org/) since quiet some time and finally made something that could be considered the de facto *Hello World* of Erlang. Unlike most of the other programming languages where concurrency is an added feature, Erlang was built around the concept of concurrent programming. If you love functional programming and can get over the weird Erlang syntax, learning Erlang will not be very difficult and is definitely worth the effort.  I have been reading [Joe Armstrong's](http://armstrongonsoftware.blogspot.in/) [Programming Erlang: Software for a Concurrent World](http://pragprog.com/book/jaerlang/programming-erlang) and will definitely recommend it to anyone starting out with Erlang. I am yet to dive into OTP, but so far, so good. :)

I have taken the naive recursive factorial algorithm and parallelized it to take advantage of multiple cores available on average system these days. Lets assume that we need to distribute the task of calculating *N* factorial over *K* processes. To do that, I have divided the set of integer {1...N} to *K* subsets, say S&#8321;, S&#8322;, ...., S<sub>k</sub>, such that 

S&#8321; &#8746; S&#8322; &#8746; ...  &#8746; S<sub>k</sub> = {1...N} and S&#8321; &#8745; S&#8322; &#8745; ... &#8745; S<sub>k</sub> = &#8709;

Where,

S&#8321; = \[ 1, K+1, 2K+1, ... , (x&#8321;)\*K+1 ];  
x&#8321; : (x&#8321;)\*K + 1 <= N and (x&#8321;+1)\*K + 1 > N and x&#8321; &#8712; &#8484; 


S&#8322; = \[ 2, K+2, 2K+2, ... , (x&#8322;)\*K+2 ];  
x&#8322; : (x&#8322;)\*K + 2 <= N and (x&#8322;+1)\*K + 1 > N and x&#8322; &#8712; &#8484;

. . .  
. . .  
. . .  

S<sub>k</sub> = \[ K, K+K, 2K+K, ... , (x<sub>k</sub>)\*K+2 ];  
x<sub>k</sub> : (x<sub>k</sub>)\*K + 2 <= N and (x<sub>k</sub>+1)\*K + 1 > N and x<sub>k</sub> &#8712; &#8484;

Product of all the elements in each of the sets in calculated in a separate thread and all these calculations, theoretically, can be done in parallel. These sub-products are then multiplied together to get the final factorial.

This is how it looks like: 
![Parallel Factorial](images/factorial.png)

\*Update\*: The algorithm described above works but is not the best way to find the factorial of a number and after some time on Google, I am convinced that [Luschny's](http://www.luschny.de/math/factorial/FastFactorialFunctions.htm) is *the* resource on the internet if you are looking for something faster and has a factorial somewhere in it.

Also, this may not be the best language to implement this and this may not the best problem to parallelize. Nonetheless, if you run the code given below you'll realize that this one makes a pretty good example of the limitations of parallelism as given by [Amdahl's Law](http://en.wikipedia.org/wiki/Amdahl's_law):

> The potential speedup gained by parallel execution of a program is limited by the portion that can be parallelized.

I am an Erlang beginner, and this could be very well a sub-optimal implementation. Do let me know, what you think of it and if you enjoyed reading this, you should follow me on [twitter](http://twitter.com/swvist).

> *Start the Erlang shell*  
> $ erl  
> *Compile the code*  
> 1> c(factorial).  
> *Call the calculate function*  
> 2> factorial:calculate(10000).


<br>
<code>
<script src="https://gist.github.com/3715155.js"> </script>
</code>
