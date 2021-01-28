# RtoPython
Some code and tricks to get from R to Python

## Tricky tricks to take care of

### R indexes start at 1, Python at 0
R
```
x <- c(1,2,3)
x[1]
# 1
```
Python
```
x = [1,2,3]
x[1]
# 2
```
As a consequence (?) iteration ends at n in R, n-1 in Python 

R
```
1:3
# 1 2 3
```
Python
```
x = range(3)
x
#range(0, 3)

[y for y in x]
# [0, 1, 2]
```
Note that, in Python, the iterator is not evaluated until used.

### R fills arrays in columns, numpy in lines
R
```
matrix(1:12, ncol=4, nrow=3)[1,]
# Note: ncol=4 is facultative
# [1] 1 4 7 10
```
The matrix is filled in columns. Hence, the first line of this matrix is 1, 4, 7, 10.

Python
```
import numpy as np
np.arange(1,13).reshape((3,4))[0,:]
# array([1, 2, 3, 4])
```
The matrix is filled in lines. Hence, the first line of this matrix is 1, 2, 3, 4.

R, Python like
```
matrix(1:12, nrow=3, byrow= TRUE)[1,]
# Note: ncol=4 is facultative
# [1] 1 4 7 10
```
Python, R like
```
np.arange(1,13).reshape((3,4),order="F")[0,:]
```

### Python's functions default parameters are evaluated only once
R
```
foo <- function(b=input) return(b)
input <- 2
foo()
#2
input <- 3
foo()
#3
```

Python
```
def foo(b = input):
    return (b)
 
input = 2
print(foo())
# 2

input = 3
print(foo())
# 2
```
### There are immutable objects in Python
int, float, bool, string, unicode and tuple objects are "immutable". They can't be changed
after they are created. Trying to modify an immutable object will lead to errors (then 
it won't usually be an issue). 
Python
```
tuple1 = (0, 1, 2, 3)  
tuple1[0] = 4
# Error
```
### Some Python functions work "in place"
R
```
list1 <- c(2, 3, 1, 0)  
sort(list1)
list1
# 2 3 1 0
```

Python
```
list1 = [2, 3, 1, 0]  
list1.sort()
list1
# [0, 1, 2, 3]
```
Use sorted if you want to keep list1
```
list1 = [2, 3, 1, 0]  
sorted(list1)
list1
# [2, 3, 1, 0]
```
### In Python, two objects can be "the same"
So when you modify one, it changes the other, while in R, the objects are "similar" 
(R makes a copy, when needed)
R
```
a <- 3
b <- a
a <- 4
b
# 3
```

Python
```
a = 3
b = a
a = 4
b
# 3

# Watch out
a = b = [3, 2]
b[0] = 4
a
# [4, 2]
```
### In Python, strings are arrays

Python
```
[x for x in "spam"]
# ['s', 'p', 'a', 'm']
```
use 
```
[x for x in ["spam"]]
# ['spam']
```


R
```
for (i in "spam") print(i)
#[1] "spam"
```

## Conversions

Python usually doesn't transform objects, so you frequently have to change objects to 
a compatible type

float to Integer: 
```
int(3.0)
```

pandas Dataframe to numpy array: df.value


## Equivalences

| R      | Python |
| ----------- | ----------- |
|&<br>&&|&<br>and|
|\|<br>\|\||\|<br>or|
| [aperm(a, perm)](https://www.rdocumentation.org/packages/base/versions/3.6.2/topics/aperm)<br>aperm(a, c(3,1,2))| [np.moveaxis(a, source, destination)](https://numpy.org/doc/stable/reference/generated/numpy.moveaxis.html)<br>np.moveaxis(a,2,0)|
| [c()](https://www.rdocumentation.org/packages/base/versions/3.6.2/topics/c)<br>c(1:3,4,6)| [numpy.r_](https://numpy.org/doc/stable/reference/generated/numpy.r_.html)<br>numpy.r_[1:3, 4, 6]<br>|
|[ifelse(test, yes, no)](https://www.rdocumentation.org/packages/base/versions/3.6.2/topics/ifelse)|[np.where(cond,x,y)](https://numpy.org/doc/stable/reference/generated/numpy.where.html)|
|indexing without loosing dimensionnality: a[1,1,drop=FALSE]|a[[1],[1]]|
| ----------- | ----------- |
|dgamma(x, shape, rate = 1)<br>note: one can use dgamma(x, shape, scale=1) but need to name the scale argument|scypi.stats.gamma(a=shape, loc=0, scale=1/rate)|
|dunif(x, min=0, max=1)|scypi.stats.uniform(loc=0, scale=1) defined on the [loc, loc+scale] interval!|
| ----------- | ----------- |

watch out: random function in Python frequently leads to nan. Example; uniform(loc=1, scale=0) or normal(loc=0, scale=0)

## rpy2

rpy2 allows to run some R code from python. Examples

```
import rpy2.robjects as robjects
import numpy as np

# Needed to get data back in numpy
from rpy2.robjects import numpy2ri
numpy2ri.activate()

# Use R function directly
print(robjects.r.runif(10))

# or as a python function
robjects.r.runif(100)
runif =  robjects.r("runif")
print(runif(4))

# import libraries and use functions
from rpy2.robjects.packages import importr
rlhs = importr('lhs')
rlhs.randomLHS(10, 3)

# use own functions
robjects.r("""
sampleInt <- function(x, size, replace = FALSE, prob=NULL){
    x[sample.int(length(x), size, replace, prob)]
    }
""")
sampleInt = robjects.r['sampleInt']
print(sampleInt(np.arange(1,11),4))
```
