# Kmers.md

This repository is a tutorial about kmers.

## Kmers in a simple string

Please use a text editor to make a fasta file called `example.fasta` that contains one sequence and looks like this:
```
>example
CCCCCCCCGGGGGGGG
```

Now use the program `jellyfish` to count the kmers in this sequence and print them

```
cat example.fasta | jellyfish count /dev/fd/0 -s 256 -m 5 -o non-canonical.jf && jellyfish dump non-canonical.jf 
```

Now lets look at what adding the -C flag does (canonical kmers are collapsed).
```
cat example.fasta | jellyfish count /dev/fd/0 -C -s 256 -m 5 -o non-canonical.jf && jellyfish dump non-canonical.jf 
```

