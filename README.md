# FuzzyMatchR
Reference page to link to R implementation of a probabilistic matching function.

If you need to link together two or more datasets that don't have a unique identifier but do have common fields, which may contain minor differences (e.g. first name, surname, address), then you are probably going to want to use a probabilistic matching approach. This approach allows for misspellings, data entry errors and missing values to give you the best possible matching result.

A high-quality implementation of this method in R, which scales well (to millions of records), can be found here: https://github.com/kosukeimai/fastLink

A paper published by the developers explaining how and why it works can be found here: http://imai.princeton.edu/research/files/linkage.pdf

NB: This is not my work! The purpose of this repository is to provide a reference point to guide you to the above source.

### Example usage ###

This example runs on one of the dummy datasets used for the examples in Robin Linacre's python-based fuzzymatcher (https://github.com/RobinL/fuzzymatcher). To see how it performs on a larger dataset, you can change "left_3" and "right_3" in the read.csv lines to "left_4" and "right_4" respectively.


```r
install.packages("fastLink")
library(fastLink)

# Get data
left <- read.csv("https://raw.githubusercontent.com/RobinL/fuzzymatcher/master/tests/data/left_4.csv")
right <- read.csv("https://raw.githubusercontent.com/RobinL/fuzzymatcher/master/tests/data/right_4.csv")

left
right

# Make sure names are consistent
names(right) <- names(left)

# Run the matching
matches.out <- fastLink(
  dfA = left, dfB = right, 
  varnames = names(left),
  stringdist.match = c("first_name", "surname", "dob","city","email"), # Specifies the variables you want to treat as strings for fuzzy matching
  partial.match = c("first_name", "surname","city","email"), # Specifes variables where you want the algorithm to check for partial matches
  verbose = T,
  threshold.match = .9 # Match probability threshold. The default is .85, and you can play around with different values
)

# Gives the match rate, estimated falst positive rate (FDR) and estimated false negative rate (FNR)
summary(matches.out)

# Extracts the matched data
a <- matches.out$matches$inds.a
b <- matches.out$matches$inds.b

matched.data <- merge(cbind(left[a,],"matchindex"=b),
                      cbind(right[b,],"matchindex"=b),
                      by="matchindex")

matched.data$score <- matches.out2$posterior

View(matched.data)

```
