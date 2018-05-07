# Introduction to R with Maine campaign finance data

This is a tutorial for complete beginners in R to start using and analyzing Maine's campaign finance data. 

[The last section](https://github.com/vigorousnorth/MaineCampaignFinance2018#prepping-the-donors-data-for-an-online-data-table) also includes some basic workflow instructions for Press Herald staff to produce interactive datatables from these public reports.


## Getting started

We'll be working with raw data from the Maine Ethics Commission. [Download and save the 2018 "Contributions and Loans" CSV file here.](https://secure.mainecampaignfinance.com/PublicSite/DataDownload.aspx) You'll need to unzip the file and save the CSV file somewhere where you can find it later (I suggest renaming it with a more useful file name). 

[Download R from here](https://cran.rstudio.com/)

[And download RStudio from here](https://www.rstudio.com/products/rstudio/download/#download)

You’ll need to drag both programs into your Applications folder when they’re downloaded.

Next, open RStudio.

You’ll see a command line where you’ll type instructions on the left, a file directory on the right, and some tabs labeled “Environment,” “History” and “Connections” in the upper right.

Type `getwd()` in the console. This is short for “get the working directory.” We want to find out where R is working from, then point it in the direction of our folder where the campaign finance data is. 

In the right side, where the files are listed, navigate to the folder where your data is saved. When you see your files, click the gear icon labelled “More” and choose “Set as working directory.” 

When you do that, the console will auto-populate with the command `setwd("[*the path to your current directory*]")`.

Now, type the up arrow twice in the console. The up arrow goes through the most recent commands you’ve entered, so hitting it twice will retrieve  `getwd()`. Hit enter and it should give you the path of the current directory where your data is.

Now we’re ready to load the campaign finance data into R. Here’s what that command will look like. Don’t type it in just yet:

`> data18 <- read.csv("2018_Contributions.csv", header=TRUE)`

This says that we’re creating a new variable – in this case it’s going to be what R calls a “data frame” – called `data18`. 

And with the arrow notation described by the `<-` characters, we’re telling R to assign the contents of our CSV file to that variable. The `header='TRUE'` parameter tells R that the column names of this new data frame are contained in the first row of the CSV file. 

Now type ONLY this into the console:

`> data18 <- read.csv("2018_`

And then, before hitting enter, hit the tab key. R will auto-complete the file name for you. This is a big time-saver that keeps you from typing out the full file name and it’s available for lots of commands and variables. 

You should now have this written out in your console:

`> data18 <- read.csv("2018_Contributions.csv", header=TRUE)`

Hit enter. It’ll take a few seconds to load the file.

When it’s done, use the “summary” command to see what it looks like:

`> summary(data18)`

`summary()` gives you, as the name suggests, a batch of summary statistics for each column in your data. 

Note that some of these columns are summarized as numbers – `ReceiptAmount` and `OrgID` for instance  – and for those the summary function returns numerical statistics like the mean, median and quartiles. 

But most of them are what R considers “factors.” They’re basically strings of text, many of which are identical. Under `City`, for instance, the most common factor will probably be “Portland,” and under `FirstName`, the most common factors will be names like “John” and “David.” 

Note that the `Date` columns are also factors – there’s no median date. There is a way to force R to recognize the date as a numerical date type, so that you can retrieve donations made before or after a certain time period, but I won’t get into that here (you can google it if you need to). 

A few other useful commands:

`> nrow(data18)`

This simply gives you the number of rows (donations) in the dataset. As of May 2 there were 19,227 rows and this number will probably be much larger by June. 

`> head(data18)`

This just gives you the first 5 rows of the data frame. It’s another handy way to do a quick check of your data, peek at its structure and make sure everything looks OK. 


## STRUCTURE OF R DATA FRAMES

When R loads a data frame, every element in it is indexed by a row and column number – kind of like rows and columns in Excel. 

Unlike Excel, you can’t just scroll through the data visually. The data sets we’re working with here are just too large to handle that way.

But R is very fast at delivering specific rows and columns from the command line, using this syntax:

`data18[*rownumbers*, *columnnumbers*]`

Try this command in the console:

`> data18[5,]`

This should return the 5th row from the data frame (note that I didn’t put any number after the comma in the command above – that way, I get the entire row, not just a specific column).

Now try these commands, and compare it with the result above:

`> data18[5,5]`

`> data18[c(5,7,10),5]`

`> data18[5,2:6]`

`> data18[5:7,]`

* As you probably guessed, the first command returned the 5th column of the 5th row of data (someone’s name from the `FirstName` column). 

* The second command was a little trickier. Instead of passing R one number for the row, I gave it a vector of three numbers. In R, a “vector” is a sequence of elements. You can define a vector using the `c()` command (I think of the “c” as an abbreviation for “combine”). So `data18[c(5,7,10),5]` returned the names (that’s the 5th column, specified after the comma) from the 5th, 7th and 10th rows (specified in the vector before the comma).

* Often, you just want a vector of sequential numbers;  that’s what the colon notation does in the 3rd command. `data18[5,4:8]` gives columns 4-8 from the 5th row.  

* Similarly, the fourth command returns rows 5 through 7 (`5:7`).

Finally, for dataframes with named columns, like we have here, R also has a convenient notation for returning the results from a column as a vector:

`> data18$ReceiptAmount[5:7]`

The `$` sign specifies a specific single column from the data frame. Note that, because each column is already one-dimensional, there is no comma after the numbers in the bracket. This simply returns the 5th through the 7th elements of the ReceiptAmount column. 


## BASIC MATH IN R

You can also use the console to do basic math:
~~~~
> 5+6
[1] 11
> 3^2
[1] 9
~~~~

You can use `<`, `>` (less/greater than), `<=`, `>=` (less/greater than or equal to), `!=` (does not equal) and `==` (does equal) operators to compare values or vectors:
~~~~
> 3 < 5
[1] TRUE
> c(1:5) >= c(0,2,4,6,8)
[1]  TRUE  TRUE FALSE FALSE FALSE
> c(1:5) != c(0,2,4,6,8)
[1]  TRUE FALSE  TRUE  TRUE  TRUE
~~~~
(in vector operations, each value of the first vector is compared against the corresponding value in the second)

For instance, is the `ReceiptAmount` in row 20 greater than the `receiptAmount` in row 10?
~~~~
 > data18$ReceiptAmount[20] > data18$ReceiptAmount[10]
[1] FALSE
~~~~

(you may get a different result since you’re working from different data)

You can also use Boolean operators to check if multiple conditions are true or false. The `&` operator is short for AND:
~~~~
> (5 < 7) & (3^2 <= 9)
[1] TRUE
~~~~
And the `|` operator is short for OR:
~~~~
> (9 < 7) | (3^2 <= 9)
[1] TRUE
~~~~
(here, the first set of parentheses is `FALSE`, but the second is `TRUE`; because one of them is true, the `OR` operator returns `TRUE`.)

And the `!` operator is short for `NOT`:
~~~~
> !(5 > 10)
[1] TRUE
> !FALSE
[1] TRUE
~~~~
 
**An important note:** if you’re checking to see if two values are equal, you need to use `==`, not `=`.

In R, the `=` sign is another way to assign a value to a variable. For instance, try this:
~~~~
> a <- 6
> a = 4
~~~~
Here, we’ve created a new variable called `a` and assigned the value `6` to it. 
You might expect the second line, `a=4`, to return `FALSE`.  But it doesn’t:
~~~~
> a
[1] 4
~~~~
The single = sign reassigns the value of `a` to 4. If we wanted to check whether `a` is a specific value we need to use a double equals sign:
~~~~
> a == 4
[1] TRUE
> a == 6
[1] FALSE
~~~~


## FILTERING AND SUBSETTING DATA FRAMES

For the next couple examples, it’ll be useful to work with a much smaller dataset. I’ve created a new dataframe using only the first 10 rows of data18 as it existed on May 2 (your version will be different), and using only the `ReceiptAmount`, `ReceiptDate`, `LastName` and `FirstName` columns (those are columns 2-5).

Here’s the code I used:

`> tenrows <- data18[0:10, 2:5]`

But, since Ethics is updating the raw data all the time, and so that we can all work from the same example here, I’ve saved this small sample in a CSV that [you can download from this repository](https://github.com/vigorousnorth/MaineCampaignFinance2018/blob/master/sample.csv). Download and save that file in whatever working directory you’re in, then use `read.csv` to load it into R:

`> sample <- read.csv("sample.csv", header=TRUE)`

Let’s check to make sure it worked. Type:

`> sample`

and hit enter. The console will print the entire contents of the `sample` variable (obviously, you wouldn’t want to do this with a 20,000 row dataset).

OK, now that we’ve got a more manageably sized dataset, we’re going to try some stuff that’s going to illustrate how vectors in R can be used to slice and filter through large datasets. 

Above we created some vectors of numbers: `c(2,5,7)`, for instance, and `data18$ReceiptAmount[5:7]`. 

But R can also make vectors out of other things. `c('red','green','blue')` is a legitimate vector in R.  `data18$City[5:7]` will give you a vector of donors’ city names from the 5th through the 7th rows of our `data18` dataframe.

Most useful for our purposes are Boolean vectors – vectors of true/false values. Try typing this into R:
~~~~
> a <- c(TRUE,FALSE,FALSE,TRUE)
> sample[,a]
~~~~
We created a vector of 4 true and false values, then, in the second line, we asked for a subset of columns from our `sample` dataset based on that vector. As you may have guessed, `sample[,a]` returns only the 1st and 4th columns of the `sample` data frame, because only the 1st and 4th elements of the a vector are `TRUE`.

Now let’s create another vector of true/false values named `b` – can you guess what this one is doing? 

`> b <- sample$ReceiptAmount > 50`

If you’re having trouble figuring out what’s going on, type `b` in the console to look at what’s in there, and compare it to the contents of `sample`.

As you’ve probably surmised, `b` is a vector of true/false values that’s `TRUE` when the `ReceiptAmount` of the donation from the corresponding row is over $50, and otherwise `FALSE`. 

And, as you may have guessed, this gives us a great way to make a subset of the `sample` dataframe, showing only the donors who have given more than $50:

`> sample[sample$ReceiptAmount > 50,]`

(the above is equivalent to typing `sample[b,]` – in both cases, we’re asking for the rows defined by a boolean vector inside the brackets and before the comma). 
 
Using similar logic, let’s find everyone in our sample dataframe who has the first name “LINDA”:

`> sample[sample$FirstName == 'LINDA',]`

Challenge: How would you get all the rows from the `sample` dataframe where the `ReceiptAmount` is less than $10 or greater than $100?



## SUBSETTING THE DONORS DATA

Now that you’ve got a handle on filtering small dataframes, let’s tackle the big one. We want to filter the big Ethics dataset of political contributions to only look at donations for active gubernatorial candidates, getting rid of all the donations to PACs and state house campaigns.

As you may recall from when we ran the `summary()` function above, there’s a column labelled CandidateName. Here’s an excerpt from the summary report using my data:
~~~~
 CandidateName   									  
                        :11590   	
 Janet Mills            : 1184  
 Mr. Adam Roland Cote   :  942            
 Hon. Mark Westwood Eves:  898            
 HON. TERESEA M HAYES   :  485            
 Shawn Moody            :  401            
 (Other)                : 3727
~~~~
This tells me that, of the complete data18 dataframe that I loaded from the CSV file, there are 11,590 rows that are blank in the `CandidateName` field (these are probably donations to PACs and/or ballot questions), plus 1,184 rows that represent donations to Janet Mills, 942 rows for Adam Cote, and so on. 

It also tells me that, if I’m filtering out all the PAC and ballot question donations, my final result should be no larger than 8,500 rows (I’m starting with just under 19,000 rows, and subtracting out 11,590 rows that have no candidate specified). Again, you’ll be dealing with more rows, but this summary data can give you a ballpark estimate.

This column offers one approach to subsetting the data: I could just use my `==` operators with an `|` (OR) Boolean statement to get all the rows where the `CandidateName` column is equal to “Janet Mills” OR “Mr. Adam Roland Cote” OR “Shawn Moody” OR... etc. etc.

(don’t actually type this in; there’s a better way)

`>  data18[((data18$CandidateName =="Shawn Moody") | (data18$CandidateName =="Janet Mills") | [and so on, and so on…]`

That’s pretty unwieldy. Also, there are some candidates whose names appear in multiple spellings in this dataset (`Teresea Hayes` and `HON. TERESEA M HAYES` for instance).

There’s a better way to do this. We’re going to look at the first column in the dataframe, the `OrgID` column. This is a unique identifier for each campaign. This way, we don’t need to worry about strange spellings in the CandidateName column.

Here are the OrgIDs for active candidates as of May:

|Candidate|OrgID
| ----- |:------:|
|Caron	|10166
|Cote	|10067
|Dion	|10184
|Eves	|10132
|Fredette	|10168
|Hayes		|10057
|Mason		|10173
|Mayhew	|10112
|Mills		|10125
|Moody		|10224
|Russell	|10139
|Sweet		|10106


Let’s put these into a vector:

`> gov_ids <- c(10166,10067,10184,10132,10168,10057,10173,10112,10125,10224,10139,10106)`

And now we can subset our original dataset on the OrgID column. Here’s one way to do it, the way we’ve already learned:

`>  data18[( (data18$OrgID == gov_ids[1]) | (data18$OrgID == gov_ids[2]) | (data18$OrgID == gov_ids[3]) | (data18$OrgID == gov_ids[4]) | (data18$OrgID == gov_ids[5]) | (data18$OrgID == gov_ids[6]) | (data18$OrgID == gov_ids[7]) | (data18$OrgID == gov_ids[8]) | (data18$OrgID == gov_ids[9]) | (data18$OrgID == gov_ids[10]) | (data18$OrgID == gov_ids[11]) | (data18$OrgID == gov_ids[12]) ) ,]`

This will work. But there’s a much more efficient method. Here, the `%in%` operator checks to see if each value on the left matches at least one element the ids vector on the right:

`> govdonors <- data18[data18$OrgID %in% gov_ids,]`

Here’s a more basic example of the %in% operator:
~~~~
> c(0:5) %in% c(0,5,10,15,20,25,30,35,40)
[1]  TRUE FALSE FALSE FALSE FALSE  TRUE
~~~~
Only the numbers 0 and 5 in the first array match values in the second array, so the %in% operator only returns TRUE for the 1st and 5th elements.

Similarly,  this vector:

`data18$OrgID %in% ids`

will only be `TRUE` where the `OrgID` in a row from `data18` matches one of the values in our `gov_ids` vector. 

So now we have a new dataframe, called `govdonors`, that should only consist of rows representing donations to one of our gubernatorial candidates.

Let’s see if it worked. Try

`> nrow(govdonors)`

If this gives you `0`, something went wrong. Our May 2 dataset has 4,775 rows for 2018.

Try also 

`> summary(govdonors, maxsum=10)`

The default `summary()` function gives you the top 5 factors for each column; by specifying `maxsum=10`, you’ll get the top 10 instead. You can set maxsum equal to whatever you want but at some point it stops being useful as a summary.

## PREPPING THE DONORS DATA FOR AN ONLINE DATA TABLE

OK! If you’ve made it this far, you’ve winnowed down a dataset of over 20,000 rows to a slightly more manageable dataset of 5,000-odd rows.  Good job!

There’s still a lot of stuff in our new `govdonors` dataframe that we don’t necessarily care about a whole lot. 

In our [online database](https://www.pressherald.com/2018/05/02/search-database-donations-maines-2018-gubernatorial-campaign/) we include columns for the donation date (column 3), the beneficiary candidate (column 19), the donation amount (column 2), the donor's full name (columns 4-7), the donor's city, state, and occupation/employer (columns 23/22) – in that order.

The donor names are contained in columns 4-6, so we're going to want to combine those. And similarly, we've been combining occupation and employer (columns 23 and 22, respectively) into one column, separated by an –.

To do this, let's create a new column to `govdonors` called `fullName`, and use the `paste()` command to combine the contents of the name columns:

`> govdonors$FullName <- paste(govdonors$FirstName,govdonors$MI, govdonors$LastName,govdonors$Suffix, sep=" ")`

This tells R to cocatenate the FirstName, MI, LastName and Suffix columns into a single string, which each element separated with a single space character.

Let's do something similar with the occupation/employer columns, using an – as the separator:

`> govdonors$employment <- paste(govdonors$Occupation,govdonors$Employer, sep="–")`

Let's look at a row in our new dataframe, with the two new columns:

`> govdonors[5,]`

You should now see the new `FullName` and `employment` columns, containing the same info that's in the FirstName, LastName and Occupation columns.  

Now let's create a new dataframe called `theTable` that's going to copy only the columns we want from the `govdonors` dataframe.

Try this:

`> govdonors[5, c(3,19,2,26,10,11,27)]`

The `c(3,19,2,26,10,11,27)` specifies the columns we want, in precisely the order we want them. 

With any luck, you should see something like this:

~~~~
   ReceiptDate        CandidateName ReceiptAmount          FullName    City State
32  01/01/2018 HON. TERESEA M HAYES           100 WILLIAM  ZARAKAS  BYFIELD    MA
                     employment
32 Consultant–THE BRATTLE GROUP
~~~~

If those are, indeed, the columns we want, let's create a new dataframe called `theTable` that copies every row from `govdonors`, but only those columns we're interested in:


`> theTable <- govdonors[, c(3,19,2,26,10,11,27)]`

Did everything make it? Try checking a few rows from your new `theTable` dataframe, or running `summary(theTable)`.

### Writing data to a new csv file

Remember the `read.csv` function? There's also a `write.csv` function that can save data from R to a new CSV file in your current working directory:

`> write.csv(theTable, file="theTable.csv", row.names=FALSE, quote=c(1,2,4,5,6,7))`

This command saves the contents of our `theTable` dataframe to a new file named `theTable.csv`. The `quote` parameter specifies that we want quotation marks around every column *except* the third one, which is the numeric donation amount. The row.names=FALSE parameter tells R that we don't have named rows for our dataset (the column names, on the other hand, will get written by default).

Now, open `theTable.csv` in your favorite text editor. It should now be very close you need to save it as a JSON file for dataTables.

### A few last cleanup items

There are still a few quirks in the text file, though. Let's clean them up:

* Remember how we separated the first name, middle initial, last name and suffix columns with a single space? Lots of these records didn't have anything in the suffix or middle initial columns, so lots of the names here have double spaces between the first and last names, and/or a trailing space after the last name. Run a find and replace for double-spaces and for spaces followed by a quote mark. 

* Lots of records in the employment column are redundant – for instance you'll see lots of entries marked "Retired–NOT EMPLOYED" and "Retired–RETIRED". I like to run a find and replace on those so that they're simply "Retired". 

Finally, to convert your csv file to JSON, replace each newline character with a set of brackets with a comma separating each row: find `\n` and replace it with `\],\n\[`. Then make sure you have containing brackets at the beginning and end of your file, and make sure there's no dangling comma on the last row:


~~~~
[
["01/01/2018","Mr. Adam Roland Cote",100,"PAUL  BERKNER ","ROME","ME","Teacher/Education–COLBY COLLEGE"],
["01/01/2018","Hon. Mark Westwood Eves",100,"HEIDI  SELDOMRIDGE ","OLYMPIA","WA","Teacher/Education–TUMWATER SCHOOL DISTRICT"],
.
.
.
["04/24/2018","Shawn Moody",50,"JOHN  KOLLER ","SCARBOROUGH","ME","Retired–RETIRED"],
["04/24/2018","Shawn Moody",1600,"WILLIAM  PLANTE ","SPRINGVALE","ME","–CHARLES PLANTE AND SON - EARTHWORK AND PAVING"]
]
~~~~

Save your bracketed JSON-format file with a file name that reflects when the data was processed – for instance, `governorDonations_02May2018.json` – then upload it to the dataTables directory on our server. 

If something's not working, try using [JSONLint](https://jsonlint.com/) to make sure your JSON is valid and there aren't any stray commas or quotation marks.

## General help with R

If you run into trouble, don't worry! Chances are someone else on the internet has also had your problem. Use Google to try to find answers to your questions. You can also find lots of helpful R users who are also journalists on the [NICAR e-mail list.](https://www.ire.org/resource-center/listservs/subscribe-nicar-l/)

[DataCamp has a good introductory course on R if you want to learn more.](https://www.datacamp.com/courses/free-introduction-to-r)




