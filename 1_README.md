# Kmers.md

This repository is a tutorial about kmers.

## Kmers in a simple string

Please connect to info and make a temporary directory in /2/scratch/
```
ssh username@info.mcmaster.ca
ssh info113
cd /2/scratch/username/
mkdir temp
cd temp
```

Please use a text editor to make a fasta file called `example.fasta` that contains one sequence and looks like this:
```
>example
CCCCCCCCGGGGGGGG
```

Now use the program `jellyfish` to count the kmers in this sequence using the `count` option, and then print them using the `dump` option.

```
cat example.fasta | jellyfish count /dev/fd/0 -s 256 -m 5 -o non-canonical.jf && jellyfish dump non-canonical.jf_0 
```

Now lets look at what adding the -C flag does (canonical=reverse/complemented kmers are combined).
```
cat example.fasta | jellyfish count /dev/fd/0 -C -s 256 -m 5 -o canonical.jf && jellyfish dump canonical.jf_0 
```

Now lets make a histogram of these counts.  This histogram will show us how many times rare k-mers are observed and how many times common k-mers are observed.

```
jellyfish histo -o test_histogram.txt canonical.jf_0 
```

Please check out the output:
```
more histogram.txt
```

Let's plot it. Please download this file to your computer
```
scp username@info.mcmaster.ca:/scratch/username/temp/test_histogram.txt .
```

In R, please set your working directory (`setwd('path')`) and plot the `test_histogram.txt` data like this:
```
dat<-read.table("test_histogram.txt")
hist<-plot(dat$V1, dat$V2, type = "h", lwd = 10, ylim=c(0,5), xlab="Count", ylab="Occurrence") 
```
Please check that this makes sense.


# Real data
Now let's look at kmer distributions from WGS data from the genomes of the largest mammalian genome and a close relative with half of the genome size.  You can downloaded two additional files to your computer like this:
```
scp username@info.mcmaster.ca:/scratch/Bio722_BJE/*genome*txt
```
You now should have two files (`biggenome_hist.txt` and `smaller_genome_hist.txt`)

You can plot each one like this (ignoring the tail for now):
```
dat <-read.table("biggenome_hist.txt")
hist<-plot(dat$V1, dat$V2, type = "h", lwd = 3,xlim=c(0,40),ylim=c(0,5e8), xlab="Count", ylab="Occurrence") 
```
```
dat <-read.table("smaller_genome_hist.txt")
hist<-plot(dat$V1, dat$V2, type = "h", lwd = 3,xlim=c(0,40),ylim=c(0,5e8), xlab="Count", ylab="Occurrence") 
```

## Estimation of genome size and coverage

We can use k-mer distributions to get a rough idea of genome size and depth of coverage because the total number of k-mers is a good approximation of genome size.  To get the total number of k-mers, we need to multiply the number of times k-mers occur by the number of k-mers that occurred that amount of times. First let's look at the infrequent k-mers, which are mostly errors. 
```
head(dat)
```
for both datasets, there are lots of kmers that are observed only once or twice. Let's ignore those. The total number of kmers can be calculated as follows:
```
sum(as.numeric(dat[3:10001,1])*as.numeric(dat[3:10001,2]))
```
But because each position is covered by more than one read (on average), we need to divide this value by the coverage to get the genome size.  This can be estimated from the peak in the k-mer distribution:
```
dat[3:20,]
```
For the large genome, the coverage is ~15X and for the smaller genome, the coverage is ~13X.  So the genome size of each is:
```
sum(as.numeric(dat[3:10001,1])*as.numeric(dat[3:10001,2]))/dat$V1[dat$V2==max(dat[3:20,])]
```
As you can see these crude estimates fall short, probably do to various technical issues (such as patchy or biased sequence coverage).

## Evidence of polyploidy and/or repetitive elements?

In the plots we made, there was no obvious signal of bimodality, which would be expected for a recent polyploid.  What about repettive elements?  Let's look at the tails of these distributions...
```
dat <-read.table("smaller_genome_hist.txt")
tail(dat)
dat <-read.table("biggenome_hist.txt")
tail(dat)
```

You can see both of these histograms have binned the counts of all kmers with >= 10001 abundances in the 10001 abundance bin. What do these counts tell us about the relative abundances of repetive elements in these two species?

## What proportion of the genome is not repetitive?

We can estimate the proportion of the genome that is not repetitive based on the proportion of the kmer histogram that roughly matches the Poisson expectation (say, the part greater than a coverage of 2 but less than a coverage of 40) and then dividing this by the genome size calculated from the total kmer histogram.

The non-repetitive portion is 
```
sum(as.numeric(dat[3:40,1])*as.numeric(dat[3:40,2]))/dat$V1[dat$V2==max(dat[3:20,])]
```

And the proportion of non-repetitive elements in the whole genoem is therefore:
```
sum(as.numeric(dat[3:40,1])*as.numeric(dat[3:40,2]))/sum(as.numeric(dat[3:10001,1])*as.numeric(dat[3:10001,2]))
```
How do these proportions differ for the big and the small genomes?
