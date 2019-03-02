# Kmers.md

This repository is a tutorial about kmers.

## Kmers in a simple string

Please use a text editor to make a fasta file called `example.fasta` that contains one sequence and looks like this:
```
>example
CCCCCCCCGGGGGGGG
```

Now use the program `jellyfish` to count the kmers in this sequence using the `count` option, and then print them using the `dump` option.

```
cat example.fasta | jellyfish count /dev/fd/0 -s 256 -m 5 -o non-canonical.jf && jellyfish dump non-canonical.jf 
```

Now lets look at what adding the -C flag does (canonical=reverse/complemented kmers are combined).
```
cat example.fasta | jellyfish count /dev/fd/0 -C -s 256 -m 5 -o canonical.jf && jellyfish dump canonical.jf 
```

Now lets make a histogram of these counts.  This histogram will show us how many times rare k-mers are observed and how many times common k-mers are observed.

```
jellyfish histo -o test_histogram.txt canonical.jf 
```

Please check out the output:
```
more histogram.txt
```

Let's plot it. Please download this file to your computer

```
scp username@info.mcmaster.ca:/scratch/Bio722_BJE/*.txt .
``

In R, please setwd('path') and plot the histogram like this:
```
dat<-read.table("test_histogram.txt")
hist<-plot(dat$V1, dat$V2, type = "h", lwd = 10, ylim=c(0,5), xlab="Count", ylab="Occurrence") 
```
Please check that this makes sense.

Now we can also look at data from entire genomes.  You probably already downloaded two additional files that were in the info directory (`biggenome_hist.txt` and `smaller_genome_hist.txt`).

You can plot each one like this:
```
dat <-read.table("biggenome_hist.txt")
hist<-plot(dat$V1, dat$V2, type = "h", lwd = 3,xlim=c(0,40),ylim=c(0,5e8), xlab="Count", ylab="Occurrence") 
```
```
dat <-read.table("smaller_genome_hist.txt")
hist<-plot(dat$V1, dat$V2, type = "h", lwd = 3,xlim=c(0,40),ylim=c(0,5e8), xlab="Count", ylab="Occurrence") 
```


