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

### R fills arrays in column, Numpy in line
R
```
matrix(1:12, ncol=4, nrow=3)[1,]
# Note: ncol=4 is facultative
# [1] 1 4 7 10
```
Python
```
import numpy as np
np.arange(1,13).reshape((3,4))[0,:]
# array([1, 2, 3, 4])
```
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
