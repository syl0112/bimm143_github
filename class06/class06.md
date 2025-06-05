# Class 6: R functions
Shuk Yee Leung (PID: A18050465)

- [1. Function basics](#1-function-basics)
- [2. Generate DNA sequence](#2-generate-dna-sequence)
- [3. Generate Protein function](#3-generate-protein-function)

## 1. Function basics

Let’s start writting our first silly function to add some numbers:

Every R function has 3 things:

- name (we got to pick this)
- input arguments (there can loads of these sperated by a comma)
- the body (the R code that does the work)

``` r
add <- function(x, y=10, z=0){
  x + y + z
}
```

I can just use this function like any other function as long as R knows
about it (i.e. run the code chunk)

``` r
add(1, 100)
```

    [1] 101

``` r
add( x=c(1,2,3,4), y=100)
```

    [1] 101 102 103 104

``` r
add(1)
```

    [1] 11

Functions can have “required” input arguments and “optional” input
arguments. The optional arguments are defined with an equals default
value (`y=10`) in the function definition.

``` r
add(x=1, y=100, z=10)
```

    [1] 111

> Q. Write a function to return a DNA sequence of a user specified
> length? Call it `genetate_dna()`

The `sample()` function can help here

``` r
#generate_dna <- function(size=5) { }

students <- c("jeff", "jeremy", "peter")

sample(students, size=5, replace=TRUE)
```

    [1] "peter"  "jeff"   "jeremy" "jeff"   "jeff"  

## 2. Generate DNA sequence

Now work with `bases` rather than `students`

``` r
bases <- c("A", "C", "G", "T")
sample(bases, size=10, replace = TRUE)
```

     [1] "T" "C" "T" "G" "T" "T" "G" "G" "G" "T"

Now I have a working ‘snippet’ of code I can use this as the body of my
first function here:

``` r
generate_dna <- function(size=5) { 
  bases <- c("A", "C", "G", "T")
  sample(bases, size=size, replace = TRUE)
}
```

``` r
generate_dna(100)
```

      [1] "T" "A" "T" "A" "T" "T" "C" "T" "A" "A" "C" "A" "A" "T" "C" "G" "C" "G"
     [19] "T" "A" "G" "G" "T" "A" "C" "A" "C" "G" "C" "A" "A" "C" "T" "T" "C" "G"
     [37] "T" "A" "A" "G" "G" "T" "T" "T" "G" "A" "G" "C" "T" "C" "G" "C" "G" "C"
     [55] "T" "G" "A" "C" "C" "A" "T" "G" "A" "T" "A" "T" "G" "G" "G" "A" "T" "G"
     [73] "C" "C" "A" "C" "G" "T" "T" "G" "C" "C" "T" "T" "A" "C" "T" "T" "A" "G"
     [91] "A" "T" "G" "T" "T" "G" "G" "T" "T" "A"

``` r
generate_dna()
```

    [1] "G" "G" "A" "T" "T"

I want the ability to return a sequence like “AGTACCTG” i.e. a one
element vector where the bases are all together.

``` r
generate_dna <- function(size=5, together=TRUE) { 
  bases <- c("A", "C", "G", "T")
  sequence <- sample(bases, size=size, replace = TRUE)
  
  if(together) {
    sequence <- paste(sequence, collapse = "")
  }
  return(sequence)
}
```

``` r
generate_dna()
```

    [1] "AAGCT"

``` r
generate_dna(together = FALSE)
```

    [1] "C" "G" "C" "T" "G"

## 3. Generate Protein function

> Q. Write a protein sequence generating function that will return
> sequences of a user specified length?

We can get the set of 20 natural amino-acids from the **bio3d** package.

``` r
aa <- bio3d::aa.table$aa1[1:20]
```

``` r
generate_protein <- function(size=5, together=TRUE) {

  ## Get the 20 amino-acids as a vector
  aa <- bio3d::aa.table$aa1[1:20]
  sequence <- sample(aa, size, replace=TRUE)
  
  ## Optionally return a single element string
  if(together){
    sequence <- paste(sequence, collapse="")
  }
  return(sequence)
}
```

> Q. Generate random protein sequences of length 6 to 12 amino acids.

``` r
generate_protein(7)
```

    [1] "AEFDHWS"

``` r
generate_protein(8)
```

    [1] "SKIPAEAR"

``` r
generate_protein(9)
```

    [1] "DKGMVWQIE"

``` r
# generate_protein(size=6:12)
```

We can fix this inability to generate multiple sequences by either
editing and adding to the function body code (e.g. a for loop) or by
using the R **apply** family of utility functions.

``` r
sapply(6:12, generate_protein)
```

    [1] "WTHRCS"       "LPWTIIA"      "NFDDYENG"     "WAFMSKQFN"    "LYVPLKFICR"  
    [6] "WPGIISEVQFN"  "DLAPMPKAMSQW"

It would cool and and useful if I could get FASTA format output.

``` r
ans <- sapply(6:12, generate_protein)
ans
```

    [1] "KPWQSG"       "VVKTWVD"      "IVSANNTQ"     "KEVRGLSRL"    "EFDLTGMGLN"  
    [6] "HHMGYFHFNEN"  "MVESCQLQTEIS"

``` r
cat(ans,sep="\n")
```

    KPWQSG
    VVKTWVD
    IVSANNTQ
    KEVRGLSRL
    EFDLTGMGLN
    HHMGYFHFNEN
    MVESCQLQTEIS

I want this to look like FASTA format with an ID line. e.g.

    >ID.6
    QRDMAD
    >ID.7
    YINTMSA
    >ID.8
    PQPMWVSD

The functions `paste()` and `cat()` can help us here…

``` r
cat( paste(">ID.", 7:12, "\n", ans, sep=""), sep="\n" )
```

    >ID.7
    KPWQSG
    >ID.8
    VVKTWVD
    >ID.9
    IVSANNTQ
    >ID.10
    KEVRGLSRL
    >ID.11
    EFDLTGMGLN
    >ID.12
    HHMGYFHFNEN
    >ID.7
    MVESCQLQTEIS

``` r
id.line <- paste(">ID.", 6:12, sep="")
id.line
```

    [1] ">ID.6"  ">ID.7"  ">ID.8"  ">ID.9"  ">ID.10" ">ID.11" ">ID.12"

``` r
id.line <- paste(">ID.", 6:12, sep="")
seq.line <- paste(id.line, ans, sep="\n")
cat(seq.line, sep="\n", file="myseq.fa")
```

> Q. Determine if these sequences can be found in nature or are they
> unique? Why or why not?

I BLASTp searched my FASTA format sequences against NR and found that
length 6 and 7 are not unique and can be found in the databases with
100% coverage and 100% identity. Length 8 is unique 87.50% identity.

Random sequences of length 8 and above are unique and can’t be found in
the databases.
