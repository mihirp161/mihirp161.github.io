---
title: "The Barnsley's Fern with Base Tools in R"
date: 2020-01-27
tags: [Miscellaneous, R]
header:
excerpt: "Complex Numbers, Linear Algebra, Fern, Fractal"
mathjax: "true"
---
After learning about the Mandelbrot set, I wanted to play around with the very a basic fractal- Barnsley's Fern. Again, fractal geometry is based on the idea of scale invariance which means that a figure is the same, or is invariant, no matter on what scale it is observed. In other words, the figure is constructed by repeating the same pattern at smaller and smaller scales.

The fern was first represented into a mathametical context by [Dr. Michael Barnsley](https://en.wikipedia.org/wiki/Michael_Barnsley). He first described it in his book "Fractals Everywhere." The fern is one of the basic examples of self-similar sets, i.e. it is a pattern that can be reproducible at any magnification or reduction. The fern code developed by Dr. Barnsley is an example of an iterated function system (IFS) to create a fractal. On a quick note, an IFS fractals, as they are normally called, can be of any number of dimensions, but are commonly computed and drawn in 2D. This type of fractal is made up of the union of several copies of itself, each copy being transformed by a function (hence "function system").

Anyhow, let's talk about how I drew this fractal in open-source R. Also, please feel free to look through the **source code** by going [here](https://github.com/ToadHanks/barnsleys_fractal).

To begin with, to make this kind of fractal, you would need some sort of 2D space, like a plane, and a container to hold transformations, like a matrix. You would start at `(0,0)`, then the new points are iteratively computed by randomly applying one of the following linear algebraic coordinate transformation-

  `starting at x = 0, y = 0 then iterate over one of these`    
  `f1: xn+1 = 0 (chosen 1% of the time)`    
  `    yn+1 = 0.16 yn`      
  `f2: xn+1 = -0.15 xn + 0.28 yn (chosen 7% of the time)`    
  `    yn+1 = 0.26 xn + 0.24 yn + 0.44`    
  `f3: xn+1 = 0.2 xn - 0.26 yn (chosen 14% of the time)`    
  `    yn+1 = 0.23 xn + 0.22 yn + 1.6`    
  `f4: xn+1 = 0.85 xn + 0.04 yn (chosen 85% of the time)`    
  `    yn+1 = -0.04 xn + 0.85 yn + 1.6`    

Underneath these matrix transformation, there is one common rule: first you do the dot product, and then you add and that gives you a next point which you store in a matrix. The resultant point would be in a 1 column, 2 rows matrix.

In R, it looks something like this-

```r
x <- rep(0, times=max_itr) #make vector of 0s
y <- x #copy the vector

# This creats f(x1,y1) = (0,0)
#run the iteration to generate next points

for (n in 2:(max_itr)) { 

# I have subtracted 1 at every iteration from n so I dont 
# have to add 1 to f(x), basically subtracted it from both 
# sides.

  #generate a random number between 0-100
  rand_num=runif(1, 0, 100) 
  
  if (rand_num < 1) { 
    x[n]<- 0
    y[n]<- 0.16*y[n-1]
  }
  else if (rand_num < 7){
    x[n]<- -0.15*x[n-1]+0.28*y[n-1]
    y[n]<- 0.26*x[n-1]+0.24*y[n-1]+0.44
  }
  else if (rand_num < 14){
    x[n]<- 0.2*x[n-1]-0.26*y[n-1]
    y[n]<- 0.23*x[n-1]+0.22*y[n-1]+1.6
  }
  else {
    x[n]<- 0.85*x[n-1]+0.04*y[n-1]
    y[n]<- -0.04*x[n-1]+0.85*y[n-1]+1.6
  }
  
}
```

As you can see, the process itself is very simple once you get the points to store at the right place in a matrix. One important thing to note is that, the random numbers are generated between `-2.1820 < x < 2.6558 and 0 ≤ y < 9.9983`. This give you the correct arch of the fern.

The plot is as simple as this-

```r
plot(x,y, pch='.', xlab= "", ylab = "", col= color)
```

1. Here is a fractal created by Dr. Michael Barnsley-

   <img src="{{ site.url }}{{ site.baseurl }}/images/barnsley/barn_original.png" alt="">

2. Here is mine, looks good I think-

   <img src="{{ site.url }}{{ site.baseurl }}/images/barnsley/fern.png" alt="">


For visual people who want to see how the fern is made, please checkout the link [here](https://www.geogebra.org/m/bQ8ppzRj)
And if you want to learn more about fern fractal, please go [here](https://en.wikipedia.org/wiki/Barnsley_fern#Construction) to read more about its process and matrix representation.


**NOTE**: Thank you very much for reading! If you discover any mistakes or want to offer any feedback, please feel free to email me.
{: .notice--success}
