# FuzzyMatchR
Reference page to link to R implementation of a probabilistic matching function.

If you need to link together two or more datasets that don't have a unique identifier but do have common fields, which may contain minor differences (e.g. first name, surname, address), then you are probably going to want to use a probabilistic matching approach. This approach allows for misspellings, data entry errors and missing values to give you the best possible matching result.

A high-quality implementation of this method in R, which scales well (to millions of records), can be found here: https://github.com/kosukeimai/fastLink

A paper published by the developers explaining how and why it works can be found here: http://imai.princeton.edu/research/files/linkage.pdf

NB: This is not my work! The purpose of this repository is to provide a reference point to guide you to the above source.

### Example usage ###

To run an example, first clone Robin Linacre's fuzzymatcher from here (just so you can use the example datasets): https://github.com/RobinL/fuzzymatcher

From the fuzzymatcher folder within this, run:

```{r}
install.packages("fastLink")
library(fastLink)

left <- read.csv('./fuzzymatcher/tests/data/left_3.csv')
right <- read.csv('./fuzzymatcher/tests/data/right_3.csv')

left
right

names(right) <- names(left)

matches.out <- fastLink(
  dfA = left, dfB = right, 
  varnames = names(left),
  stringdist.match = c("first_name", "surname", "dob","city","email"), # Specifies the variables you want to treat as strings for fuzzy matching
  partial.match = c("first_name", "surname","city","email"), # Specifes variables where you want the algorithm to check for partial matches
  verbose = T,
  threshold.match = .9 # Match probability threshold. The default is .85, and you can play around with different values
)

summary(matches.out2)

a <- matches.out$matches$inds.a
b <- matches.out$matches$inds.b

matched.data <- merge(cbind(left[a,],"matchindex"=b),
                      cbind(right[b,],"matchindex"=b),
                      by="matchindex")

matched.data$score <- matches.out2$posterior

View(matched.data)
matches.out$posterior`
```
