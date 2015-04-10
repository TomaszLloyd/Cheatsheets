## R Programming

##### Download a file
``` r
fileUrl <- "https://api.example.com/table.csv?access=DOWNLOAD"
download.file( fileUrl , destfile = "./data/table.csv", method="curl")
list.files("./data")

# create directory
dir.create('dir')

# check if directory or file exists - boolean
file.exists('file.csv')

# read data
# these are the important variables for read.table()
read.table('file', header = FALSE, sep = "", row.names, nrows = -1)

# options
# na.strings 	- set character respresenting missing value
# nrows 		- how many rows of the file to read
# skip 			- number of lines to skip before reading
# quote 		- where there are any quoted values ( setting quote="" solves a lot of problems usually )

read.csv(file, header = TRUE, sep = ",", quote = "\"", dec = ".", fill = TRUE, comment.char = "", ...)

read.csv2(file, header = TRUE, sep = ";", quote = "\"", dec = ",", fill = TRUE, comment.char = "", ...)

read.delim(file, header = TRUE, sep = "\t", quote = "\"", dec = ".", fill = TRUE, comment.char = "", ...)

read.delim2(file, header = TRUE, sep = "\t", quote = "\"", dec = ",", fill = TRUE, comment.char = "", ...)

# get info and first few lines of file or variable
head('file')
```
##### Parsing Excel files
``` r
library(xlsx)
read.xlsx( 'file.xlsx', sheetIndex=1, header=TRUE )
# optional params colIndex=colIndex, rowIndex=rowIndex for getting subset

# to write to xlsx
write.xlsx()
# read.xlsx2() is faster but not suited for subsets
# store in .csv or .tab files or database for distribution
```
##### Parsing XML & HTML
``` r
library(XML)
doc <- xmlTreeParse(fileUrl, useInternal=TRUE)	# parse an XML document
doc <- htmlTreePars(firlUrl, useInternal=TRUE) 	# same as above but for HTML file
rootNode <- xmlRoot( doc ) 						# gets wrapper element
xmlName(rootNode) 								# gets name of node

# to access items
rootNode[[1]]									# gets first element or array
rootNode[[1]][[1]]								# gets first element or array, then first sub element or array

xpathSApply( rootNode, xmlValue )				# programatically extract parts of a file

# XPath
/node 											# top level node
//node 											# node at any level
node[@attr-name] 								# node with attribute name
node[@attr-name='bob'] 							# node with attribute name attr-name = "bob"

xpathSApply( rootNode, "//name", xmlValue )		# gets value of all attributes with name of "name"
xpathSApply( rootNode, "//price", xmlValue )
xpathSApply( doc, "//li[@class='team-name']", xmlValue) # use this format to parse HTML
```
##### Parsing JSON data
``` r
library(jsonlite)
jsonData <- fromJSON("http://api.github.com/users/tomaszfoster/repos")
names(jsonData)

###### output
 [1] "id"                "name"              "full_name"         "owner"             "private"          
 [6] "html_url"          "description"       "fork"              "url"               "forks_url"        
[11] "keys_url"          "collaborators_url" "teams_url"         "hooks_url"         "issue_events_url" 
[16] "events_url"        "assignees_url"     "branches_url"      "tags_url"          "blobs_url"        
[21] "git_tags_url"      "git_refs_url"      "trees_url"         "statuses_url"      "languages_url"    
[26] "stargazers_url"    "contributors_url"  "subscribers_url"   "subscription_url"  "commits_url"      
[31] "git_commits_url"   "comments_url"      "issue_comment_url" "contents_url"      "compare_url"      
[36] "merges_url"        "archive_url"       "downloads_url"     "issues_url"        "pulls_url"        
[41] "milestones_url"    "notifications_url" "labels_url"        "releases_url"      "created_at"       
[46] "updated_at"        "pushed_at"         "git_url"           "ssh_url"           "clone_url"        
[51] "svn_url"           "homepage"          "size"              "stargazers_count"  "watchers_count"   
[56] "language"          "has_issues"        "has_downloads"     "has_wiki"          "has_pages"        
[61] "forks_count"       "mirror_url"        "open_issues_count" "forks"             "open_issues"      
[66] "watchers"          "default_branch"   
###### end output

myjson <- toJSON(iris, pretty=TRUE)		# pretty=TRUE shows nicely formatted JSON
cat(myjson)

iris2 <- fromJSON(myjson) 				# send the data back to a data.frame from a json file
```
##### Data tables
``` r
library(data.table)
# create data tables just like data frames
# example data frame
DF = data.frame( x = rnorm(9), y = rep( c("a","b","c"), each = 3), z = rnorm(9) )
# example data table
DT = data.table( x = rnorm(9), y = rep( c("a","b","c"), each = 3), z = rnorm(9) )

tables() 	# tells you name, num rows, num cols, how many MB, if there is a key

# For subsetting, when you only pass one variable, unlike data frames, it will use that value for number of rows
DT[ c( 2, 3 ) ] 	# this will give me the second and third rows

# for subsetting columns it is modified for data tables
DT[ , c( 2, 3 ) ]
DT[ , someFunction(x) ]				# you can pass functions!
DT[ , list( sum(x), mean(y) ) ]		# you can return sum of x values and mean of y values
DT[ , w:=z^2 ]						# you can add columns FAST. here, new w columns = square of z values
```
Be careful when copying data tables. When you copy a data FRAME `DF2 <- DF` it makes a copy whereas
when you copy a data TABLE `DT2 <- DT` it's not a copy, it's a pointer. Changes made to `DT` will show up in `DT2`!

You can also do multiple operations with Data Tables
``` r
DT[, m:={ tmp <- (x+z) ; log2( tmp + 5 ) }]
```
which returns the `log2( x + z + 5 )` since in functions in R, we return the value of the last expression

Special variable `.N` is a faster in `DT[, .N, by=x ]` than equivalent function `DT$x`

Subsetting and sorting data tables is much faster than in data frames

##### Joins
You can use keys to facilitate joining two data tables
``` r
DT1 <- data.table( x=c( "a", "a", "b", "dt1"), y=1:4 )
DT2 <- data.table( x=c( "a", "b", dt2"), z=5:7 )

setkey(DT1, x); setkey(DT2, x)
merge(DT1, DT2)

# returns this data table
   x  y  z
1: a  1  5
2: a  2  5
3: b  3  6
```
Reading a data.table is much faster than reading a data.frame. It's faster and more efficient but requires
more care in use, however you can do more with data.frames