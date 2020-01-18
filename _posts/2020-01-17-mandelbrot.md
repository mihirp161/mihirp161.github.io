---
title: "Creating the Mandelbrot Set Using Algebra"
date: 2020-01-17
tags: [Miscellaneous, R]
header:
excerpt: "Complex Numbers, Mandelbrot Set, Fractal"
mathjax: "true"
---
In this project I wanted to play around with the very first fractal- Mandelbrot Set. Fractals are bascically geometric shapes that when divided into parts, each part would be a smaller replica of the whole shape just like the atoms, or fiber in leaves, or the human dna. Fractals let us visualize nature in some sense. Of course, if you're talking about fractals, then you have to start with the "father of fractals," [Dr. Benoit Mandelbrot](https://en.wikipedia.org/wiki/Benoit_Mandelbrot#Developing_"fractal_geometry"_and_the_Mandelbrot_set). He was first to coin this bizarre term, and explained how we can mimic and see patterns in nature and even patterns present in cosmos. Anyways, let's talk about how I drew this fractal.

Please feel free to look through the **source code** [here](https://github.com/ToadHanks/Story_de_Hass_Avocados_en_2020).

A Mandelbrot is a set of numbers that display unusual properties. So, I started out by by making function that would generate those numbers by interating in a complex number plain (don't worry if your terminology is weak, I try my best to explain everything as I go), like this-

```r
for(i in x){ 
  for(j in f(x)){
  # here we loop through the function 
  # that has complex numbers list
    f_x= mandelbrot(i,j) 
  # extra code
  }
}
```
The `mandelbrot()` function above contains an expression that creats set of numbers that behave differently if the x and y are between `(-2, 1)`. Formally, `F= {x^2+c|x is any number, and -2<x<1}` in a proper code form I have something like this-

```r
mandelbrot <<- function(i,j){
  # extra code
  x <- i-c 
  y <- 240-c 
  a <- complex(real = x/200, imaginary = y/200) #real numbers
  b <- complex(real = 0, imaginary = 0) #imaginary numbers
  # extra code
  b <- b*b+a  # expression
  # extra code
}
```
The reason why unusual properties occur between -2 and 1 is due to two simple rules of algebra-    
1. Product of two negative numbers is a postive number.
2. Adding a negative is similar to subtracting that number's positive value.    
    
These two rules work against each other, so when we have the expression `F= {x^2+c|x is any number, and -2<x<1}` and we iterate many complex numbers over this expression under the limit `(-2,1)`, then we see sort of chaotic behaviour, where the output bounces back between a small segement of a number line, never going too far from 0, and never hitting the exact same value twice.    

Because real numbers get exponentially large, we can use the complex numbers. Complex numbers do not sit on the number line, thus preventing us from any infite sequence. 

A complex number is bascially a real number added to any imaginary number. Imaginay numbers are not really imginary, they have to follow same underlying rule as the real numbers. So, when we add them two together, the result is not real nor imginary, it is complex. 

Anyhow, getting back to the track, since complex numbers don't really work well with 0s and 1s of computers, we have to convert the complex numbers to their standard form of expression by factoring out the -1.
For example:    
`=> 2 + sqrt(-9)`   
`=> 2 + sqrt(9 x -1)`      
`=> 2+ sqrt(9) x sqrt(-1)`    
`=> 2+3 x sqrt(-1)`       
`=> 2+3i`      

So, whenever the complex numbers are broken down in $$A+Bi$$ form, they can be easily used in a calculation and get ordered in the complex plain.

A complex plain is just an 2-D extension of a real numbers plain, where the X-axis is a real number line, and Y-axis is an imaginary line (which mimics like a real number line). In the complex plain, each complex number has their unique address like `(1, 1+1i)`, `(1, 1+2i)` and so on.

In code, this is how I am actually doing-
```r
mandelbrot <<- function(i,j){
 # extra code
  x <- i-c 
  y <- 240-c 
  a <- complex(real = x/200, imaginary = y/200) #real numbers
  b <- complex(real = 0, imaginary = 0) #imaginary numbers
  
  for(...){
    b <- b*b+a # iterate over expression
    # extra code
    }
  }
 # extra code
}
```
Therefore whenever any number that resides beween `(-2,1)`, in a complex plain that doesn't cause an exponential growth, and become circle with radius 2. In both graphs below, this nature is in black color.  

1. Here is a fractal created by Dr.Benoit Mandelbrot-
   <img src="{{ site.url }}{{ site.baseurl }}/images/mandelbrot/original_fractal.jpg" alt="">

2. Here is mine, looks very bad-
   <img src="{{ site.url }}{{ site.baseurl }}/images/mandelbrot/mandelbrot_bad.png" alt="">

Now you might be wondering, why is there varying color hue around the black regions? In a nutshell, region with similar color are the numbers that have escaped the black region. Meaning, they are all the numbers which are not part of the set 
`F= {x^2+c|x is any number, and -2<x<1}` with given iteration. As we increased the iterations, we get more and more numbers escaping, thus creating the Mandelbrot fractal. 

Even though code for this looks small (and unoptimized), it took 4-5 hrs to get the graph done. If you liked this post, and want to learn more about Mandelbrot Set, please go [here](https://en.wikipedia.org/wiki/Mandelbrot_set) to read more about them.
