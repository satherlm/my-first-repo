---
title: "R In Class Exercises Oct 29--In class assignment week 2, part 2. A worked example using control flow (for loops, if statements, etc)"
author: "Leah Sather (partner: Emma Mulholland)"
date: "October 29, 2018"
output: 
  html_document:
    keep_md: yes
    number_sections: yes

---
# Introduction
Let's do a little exercise integrating some of the things we have learned. Here are some Illumina HiSeq reads for one of our recent projects:


```r
read_1 <- "CGCGCAGTAGGGCACATGCCAGGTGTCCGCCACTTGGTGGGCACACAGCCGATGACGAACGGGCTCCTTGACTATAATCTGACCCGTTTGCGTTTGGGTGACCAGGGAGAACTGGTGCTCCTGC"

read_2 <- "AAAAAGCCAACCGAGAAATCCGCCAAGCCTGGCGACAAGAAGCCAGAGCAGAAGAAGACTGCTGCGGCTCCCGCTGCCGGCAAGAAGGAGGCTGCTCCCTCGGCTGCCAAGCCAGCTGCCGCTG"

read_3  <- "CAGCACGGACTGGGGCTTCTTGCCGGCGAGGACCTTCTTCTTGGCATCCTTGCTCTTGGCCTTGGCGGCCGCGGTCGTCTTTACGGCCGCGGGCTTCTTGGCAGCAGCACCGGCGGTCGCTGGC"
```

Question 1. what species are these sequences from?

**Answer:** *Drosophila melanogaster*

Question 2. Put all three of these reads into a single object (a vector).  What class will the vector `reads` be? Check to make sure! How many characters are in each read (and why does `length()` not give you what you want.. try...)


```r
reads <- c(read_1, read_2, read_3)
nchar(reads)
```

```
## [1] 124 124 124
```

```r
class(reads)
```

```
## [1] "character"
```
You can't use `length()` to give character numbers because it will give the number of objects in the vector instead.

Question 3. Say we wanted to print each character (not the full string) from read_1, how do we do this using a for loop? You may wish to look at a function like `strsplit()` to accomplish this (there are other ways.)

Replace the blanks.

```r
read_1_split <- strsplit(read_1, split = "", fixed = T) 
print(read_1_split)
```

```
## [[1]]
##   [1] "C" "G" "C" "G" "C" "A" "G" "T" "A" "G" "G" "G" "C" "A" "C" "A" "T"
##  [18] "G" "C" "C" "A" "G" "G" "T" "G" "T" "C" "C" "G" "C" "C" "A" "C" "T"
##  [35] "T" "G" "G" "T" "G" "G" "G" "C" "A" "C" "A" "C" "A" "G" "C" "C" "G"
##  [52] "A" "T" "G" "A" "C" "G" "A" "A" "C" "G" "G" "G" "C" "T" "C" "C" "T"
##  [69] "T" "G" "A" "C" "T" "A" "T" "A" "A" "T" "C" "T" "G" "A" "C" "C" "C"
##  [86] "G" "T" "T" "T" "G" "C" "G" "T" "T" "T" "G" "G" "G" "T" "G" "A" "C"
## [103] "C" "A" "G" "G" "G" "A" "G" "A" "A" "C" "T" "G" "G" "T" "G" "C" "T"
## [120] "C" "C" "T" "G" "C"
```

Question 4. What kind of object does this return? How might we make it a character vector again?

```r
str(read_1_split)
```

```
## List of 1
##  $ : chr [1:124] "C" "G" "C" "G" ...
```
This returns a list. We can make it a character vector again by using: 

```r
read_1_split_vec <- as.character(unlist(read_1_split))
str(read_1_split_vec)
```

```
##  chr [1:124] "C" "G" "C" "G" "C" "A" "G" "T" "A" "G" "G" "G" "C" "A" ...
```

Question 5. How about if we wanted the number of occurrences of each base? Or better yet, their frequencies? You could write a loop, but I suggest looking at the help for the `table()` function... Also keep in mind that for for most objects `length()` tells you how many elements there are in a vector. For lists use `lengths()` (so you can either do this on a character vector or a list, your choice)

```r
base_count <- table(read_1_split_vec)
base_freq <- base_count/length(read_1_split_vec)
print(base_count)
```

```
## read_1_split_vec
##  A  C  G  T 
## 23 35 40 26
```

```r
print(base_freq)
```

```
## read_1_split_vec
##         A         C         G         T 
## 0.1854839 0.2822581 0.3225806 0.2096774
```

Question 6. How would you make this into a nice looking function that can work on either  a list or vectors of characters? (Still just for a single read)

```r
base_freqer <- function(x) {
  if (class(x) == "list") {
    x <- unlist(x)
  }
  return(table(x)/length(x))
}
base_freqer(read_1_split)
```

```
## x
##         A         C         G         T 
## 0.1854839 0.2822581 0.3225806 0.2096774
```

```r
base_freqer(read_1_split_vec)
```

```
## x
##         A         C         G         T 
## 0.1854839 0.2822581 0.3225806 0.2096774
```
This function returns base frequencies from either a list or a vector.  

Question 7. Now how can you modify your approach to do it for an arbitrary numbers of reads? You could use a loop or use one of the apply like functions (which one)?

```r
#unfinished 
#for (x in reads_in):
 # x_split <- as.character(unlist(strsplit(x, split = "", fixed = T)))
```
as.character(unlist(x))


Question 8. Can you revise your function so that it can handle the input of either a string as a single multicharacter vector, **or** a vector of individual characters **or** a list? Try it out with the vector of three sequence reads (`reads`).  
