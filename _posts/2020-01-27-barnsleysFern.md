---
title: "Simulating Barnsley's Fern Fractal"
date: 2020-01-27
tags: [Math & Stats, R]
header:
excerpt: "Complex Numbers, Linear Algebra, the Barnsley Fern, Fractal"
mathjax: "true"
---
Mathematics can capture nature's most elegant patterns. In this project, I recreated one of the most striking examples: Barnsley's Fern, a fractal that perfectly mirrors the geometric beauty found in natural ferns.

Full implementation available [here](https://github.com/opendatasurgeon/BarnsleysFractal_r).

**What is it?**

Named after Dr. Michael Barnsley, who first described it in his book "Fractals Everywhere," this fern is a perfect example of a self-similar set - a pattern that looks identical at any magnification level. It's created using an iterated function system (IFS), which builds complex patterns from simple repeated transformations.

**The Mathematics Behind the Magic**

The fern emerges from four simple coordinate transformations, each chosen with different probabilities:

1. Stem function (1% probability):
   
   `f1: xn+1 = 0, yn+1 = 0.16 yn`

2. Smaller leaflet (7% probability):       
  `f2: xn+1 = -0.15 xn + 0.28 yn, yn+1 = 0.26 xn + 0.24 yn + 0.44`

3. Left leaflet (14% probability):
    
   `f3: xn+1 = 0.2 xn - 0.26 yn,  yn+1 = 0.23 xn + 0.22 yn + 1.6`
  
4. Main leaflet (85% probability):
    
   `f4: xn+1 = 0.85 xn + 0.04 yn, yn+1 = -0.04 xn + 0.85 yn + 1.6`
                
**Implementation in R**

Here's how we bring these mathematical transformations to life:

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

A key detail - points are generated within specific bounds (-2.1820 < x < 2.6558 and 0 ≤ y < 9.9983) to create the proper arch of the fern's rachis (stalk).

**The Beautiful Result**
The final fern emerges with a simple plot command:

```r
plot(x,y, pch='.', xlab= "", ylab = "", col= color)
```

<img src="{{ site.url }}{{ site.baseurl }}/images/barnsley/fern.png" alt="">

Want to see the fern's creation in action? Check out this interactive [visualization](https://www.geogebra.org/m/bQ8ppzRj). For a deeper dive into the mathematics, visit the [Wikipedia](https://en.wikipedia.org/wiki/Barnsley_fern#Construction) article.


**NOTE**: Found a mistake or have feedback? I'd love to hear from you - please reach out via email!
{: .notice--success}
