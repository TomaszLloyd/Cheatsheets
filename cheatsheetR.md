## R Programming

##### Basics
``` r
# install packages
install.packages('package-name')

# load packages
library('package-name')

# get header information of data or file
head('data')

# get tail information of data or file
tail('data')

help(topic) # documentation on topic

ls() #shows objects in search path
dir() #shows files in current directory
getwd() # print working directory
setwd('path/to/wherever') # set working directory

cat(..., file="", sep=" ") # prints concatenated data and coerces to type

format(x, ...) # format an object for pretty printing

c(...) # combine objects to form a vector

from:to # generators a sequence

list(...) # generate a list of named or unnamed arguments
```

##### Slicing data

```r
## indexing vectors 

x[n] 							# get nth element
x[-n] 							# all but the nth element
x[1:n]							# first n elements
x[-(1:n)]						# elements from n+1 to end
x[c(1,2,4)]						# specific elements
x["name"]						# element named "name"
x[ x > 3]						# all elements of x greater than 3
x[ x > 3 & x < 5] 				# elements between 3 and 5
x[ x %in% c("a","and","the")]  	# elements in the given set

## indexing lists

x[n] 							# list with elements n
x[[n]] 							# nth element of the list
x[["name"]] 					# element of the list named "name"
x$name 							# id

##  indexing matrices

x[i,j] 							# element at row i, column j
x[i,] 							# row i
x[,j] 							# column j
x[,c(1,3)] 						# columns 1 and 3
x["name",] 						# row named "name"

## indexing data frames (matrix indexing plus the following)

x[["name"]] 					# column named "name"
x$name 							# id

```

##### Variable Conversion
```r

as.array(x), as.data.frame(x), as.numeric(x),
as.logical(x), as.complex(x), as.character(x),
# ... convert type; for a complete list, use methods(as)

```

##### Variable Information
```r
is.na(x), is.null(x), is.array(x), is.data.frame(x),
is.numeric(x), is.complex(x), is.character(x), ... 
# test for type; for a complete list, use methods(is)

length(x) 					# number of elements in x
dim(x) 						# Retrieve or set the dimension of an object; dim(x) <- c(3,2)
dimnames(x) 				# Retrieve or set the dimension names of an object
nrow(x) 					# number of rows; NROW(x) is the same but treats a vector as a onerow matrix
ncol(x) and NCOL(x) 		# id. for columns
class(x) 					# get or set the class of x; class(x) <- "myclass"
unclass(x) 					# remove the class attribute of x
attr(x,which) 				# get or set the attribute which of x
attributes(obj) 			# get or set the list of attributes of obj
```
##### Data Selection and Manipulation

```r
which.max(x) 				# returns the index of the greatest element of x
which.min(x) 				# returns the index of the smallest element of x
rev(x) 						# reverses the elements of x
sort(x) 					# sorts the elements of x in increasing order; 
rev(sort(x))				# to sort in decreasing order
cut(x,breaks) 				# divides x into intervals (factors); breaks is the number of cut intervals 
							# or a vector of cut points
match(x, y) 				# returns a vector of the same length than x with the elements of x 
							#which are in y (NA otherwise)
which(x == a) 				# returns a vector of the indices of x if the comparison operation 
							# is true (TRUE), in this example the values of i for which x[i] == a 
							# (the argument of this function must be a variable of mode logical)
choose(n, k) 				# computes the combinations of k events among n repetitions = n!/[(n−k)!k!]
na.omit(x) 					# suppresses the observations with missing data (NA) (suppresses the
							# corresponding line if x is a matrix or a data frame)
na.fail(x) 					# returns an error message if x contains at least one NA
unique(x) 					# if x is a vector or a data frame, returns a similar object but with
							# the duplicate elements suppressed
table(x) 					# returns a table with the numbers of the differents values of x
							# (typically for integers or factors)
subset(x, ...) 				# returns a selection of x with respect to criteria (...,
							# typically comparisons: x$V1 < 10); if x is a data frame, the option
							# select gives the variables to be kept or dropped using a minus sign
sample(x, size) 			# resample randomly and without replacement size elements
							# in the vector x, the option replace = TRUE allows to resample
							# with replacement
prop.table(x,margin=) 		# table entries as fraction of marginal table
```