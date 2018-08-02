# Tutorial on sentiment analysis evalutaion
## David Garcia. Aug 1st, 2018

In this exercise we will learn how to use Syuzhet for social media texts. We will use evaluation datasets of annotated text to compare Syuzhet against GoogleNLP in Youtube and BBC Forum datasets.


### Tasks:

1. Download and prepare evaluation texts

2. Run Syuzhet over evaluation texts

3. Evaluate against sentiment annotations and compare with Google NLP

4. Conclusions

## 1. Download and prepare evaluation texts

**1.1 Download annotated datasets**

Download and unzip the files that you can find at http://sentistrength.wlv.ac.uk/documentation/6humanCodedDataSets.zip. 

```r
download.file("http://sentistrength.wlv.ac.uk/documentation/6humanCodedDataSets.zip", 
              destfile = "SentiStrengthData.zip")
unzip("SentiStrengthData.zip")
```

**1.2 Read the BBC and Youtube files**

Read into a data frames the content of bbc1000.txt. Take into account that the file is in latin-9 encoding (fileEncoding="latin-9"). After reading the data, convert the text to ASCII with the iconv function.


```r
bbcdf <- read.csv(file="bbc1000.txt", header=TRUE, sep="\t",quote="", 
                  stringsAsFactors=FALSE, fileEncoding="latin-9")
colnames(bbcdf)<- c("pos","neg","text")
bbcdf$text <- iconv(bbcdf$text, from="latin-9", to="ASCII")
```

Repeat the process for the YouTube3407.txt, saving it in another data frame.


```r
ytdf <- read.csv(file="YouTube3407.txt", header=TRUE, sep="\t",quote="", 
                 stringsAsFactors=FALSE, fileEncoding="latin-9")
colnames(ytdf)<- c("pos","neg","text")
ytdf$text <- iconv(ytdf$text, from="latin-9", to="ASCII")
```


## 2. Run Syuzhet over evaluation texts

**2.1 Install and load the syuzhet package**
Install the syuzhet package. 


```r
#install.packages("syuzhet")
library(syuzhet)
```

**2.2 syuzhet test**

Run the get_sentiment function of syuzhet over the first 5 examples of the BBC dataset. Print the text, the result of syuzhet, and the annotations of each text.


```r
for (i in seq(1,5))
{
  text <- as.character(bbcdf$text[i])
  print(text)
  print(get_sentiment(text))
}
```

```
## [1] "\"Where were the suicide pilots from? Where is Bin Laden from? Where is the Wahhabist philosophy that inspires these people from?\"  Where are almost all terrorists from?  The Middle East, right?  And where are the Coalition troops?  The Middle East, right?  It's time you started thinking of the entire area rather then specific countries. The whole place is riddled with nutcases, and with just a little more time and prodding you might have eventually arrived at that conclusion on your own."
## [1] 0.7
## [1] "Smoker - Mainland Britain consists of England,Scotland and Wales so it is possible.Why didn't Bush and Blair sort out how to achieve democracy BEFORE invading Iraq?It could have saved up to 650,000 lives."
## [1] 1.95
## [1] "Hmmm.....the guy who took it with his government is now the number one terrorist. Everyone who take these question with the government, hands up on american terror list, just as any anyone who shakes hands with rumsfeld in middle east, becomes a terrorist, a few years later.&gt;&gt;&gt;&gt;  So are you saying they're 'regular' Muslims?"
## [1] -2.5
## [1] "     Yes, let's NOT forget that there are 2 people on the flight deck on this aircraft and the co-pilot would also have had his hands full doing his part. I get so annoyed when they only ever refer to the 'Hero Pilot'.  It is remarkable that there were no casualties and for this we must all be very thankful.  On a different tack, I can just see the lawyers rubbing their hands in glee for the broken finger nail damages etc. Watch this space for the law suits.........guaranteed   "
## [1] 0.8
## [1] "He's a religious fanatic which is worse than your average cretin."
## [1] -1.25
```


**2.3 Apply Syuzhet to all texts**

For each text in the BBC and YouTube datasets, run the get_sentiment function of Syuzhet. Use sentence detection for the BBC texts (they are long) but not for YouTube (they are short). When you get the results for each text, your are interested on an average measure over the sentences.


```r
bbcSyuzhet <- NULL
for (text in bbcdf$text)
{
  pol <- get_sentiment(text)
  bbcSyuzhet <- c(bbcSyuzhet, mean(pol))
}

ytSyuzhet <- NULL
for (text in ytdf$text)
{
  pol <- get_sentiment(text)
  ytSyuzhet <- c(ytSyuzhet, pol)
}
```

## 3. Evaluate against sentiment annotations and compare with Google NLP

**3.1 Load Google NLP data**

Read the files bbc1000-GoogleScore.csv and YouTube3407-GoogleScore.csv into two vectors. Take into account that they do not have any header and they only contain numeric data.

```r
Gbbc <- as.vector(read.table("bbc1000-GoogleScore.csv", header=F, colClasses = "numeric")$V1)
Gyt <- as.vector(read.table("YouTube3407-GoogleScore.csv", header=F, colClasses = "numeric")$V1)
```


**3.2 Build data frames**

For each dataset, construct a data frame with four columns. The first two columns contain Boolean values that are TRUE if the positive and negative score of the human annotation of each text is 3 or more. Store the two sentiment analysis values (Syuzhet and GoogleNLP) in the remaining columns. Save the data frames in files for the next task.


```r
bbc <- NULL
bbc$pos <- bbcdf$pos >= 3 
bbc$neg <- bbcdf$neg >= 3
bbc$syuzhet <- bbcSyuzhet
bbc$google <- Gbbc
save(bbc, file="bbc.RData")

yt <- NULL
yt$pos <- ytdf$pos >= 3
yt$neg <- ytdf$neg >= 3
yt$syuzhet <- ytSyuzhet
yt$google <- Gyt
save(yt, file="yt.RData")
```


**3.3 Evaluate on YouTube**

Install and load the pROC package. Load the data frame for YouTube. Plot the ROC of the classification as positive for Syuzhet and GoogleNLP in the same plot (you can combine plots with the add=TRUE parameter). Print the results to find the predictors with the best AUC. Repeat the task for the classification as negative (change the sign of the Syuzhet score for this prediction), getting AUC values too.


```r
#install.packages("pROC")
library(pROC)
```

```
## Type 'citation("pROC")' for a citation.
```

```
## 
## Attaching package: 'pROC'
```

```
## The following objects are masked from 'package:stats':
## 
##     cov, smooth, var
```

```r
load("bbc.RData")
load("yt.RData")

#Your code here
```

**3.4 Evaluate on BBC**

Repeat the above task for the BBC data frame.


```r
#Your code here
```

# 4. Conclusions

1. What was the best performing method for Youtube?

2. What was the best performing method for BBC forums?

3. If there is a difference or similarity, what property of the methods can explain it?

4. Do you observe any differences between prediction of positive and negative sentiment?
