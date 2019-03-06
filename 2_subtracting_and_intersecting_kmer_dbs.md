# Subtracting and intersecting k-mer databases

OK, let's mess around with some k-mers from an African claed frog species in the genus *Xenopus*

# Extract a scaffold for analysis

We can extract the scaffold corresponding to the sex chromosome like this:
```
 perl -ne 'if(/^>(\S+)/){$c=grep{/^$1$/}qw(Chr8L)}print if $c' Xbo.v1.fa > XB_Chr8L.fasta 
```

# Split up the fasta file
 I wrote a script to split up a fasta file into smaller files that are consecutively named, each with a defined length. The file is called `Split_fasta.pl`:
 
```
#!/usr/bin/env perl
use strict;
use warnings;

# This program reads in a fasta file and splits it into several
# smaller files of length equal to $ARGV[1].  
# This is useful for doing kmer quantification of different parts of a chromosome
# Output files will be printed to $ARGV[2], which is the "output_directory"
# First calculate how many lines the input fasta file is. The divide this number
# by the number of bits you want to divide it into to get the $ARGV[1] length

# Run this script like this
# Split_fasta.pl input.fasta length output_directory 


my $inputfile = $ARGV[0]; # This is the input file
my $length = $ARGV[1]; # this how many lines each subset file will have
my $outputdirectory = $ARGV[2]; # This is where the bits will be printed

unless (open DATAINPUT, $inputfile) {
	print 'Can not find the input file.\n';
	exit;
}

my $filecounter=0;
my $linecounter=0;
my $header;
my @temp;
my $outputfile;
my $line;

# Read in datainput file

while ( my $line = <DATAINPUT>) {
	if($line =~ '>'){
		# save the fasta HEADER for later to print in each output file
		$filecounter+=1;
		# parse header
		chomp($line);
		@temp=split('>',$line);
		$header=$temp[1];
		unless (open(OUTFILE, ">".$outputdirectory."\/".$header."_".$filecounter))  {
			print "I can\'t write to $outputfile\n";
			exit;
		}
		print OUTFILE ">".$header."_".$filecounter."\n";
		$linecounter=0;
	}
	elsif($linecounter < $length){
		print OUTFILE $line;
		$linecounter+=1;
	}	
	else{	
		$filecounter+=1;
		unless (open(OUTFILE, ">".$outputdirectory."\/".$header."_".$filecounter))  {
			print "I can\'t write to $outputfile\n";
			exit;
		}
		print OUTFILE ">".$header."_".$filecounter."\n";
		print OUTFILE $line;
		$linecounter=0;
	}
}		

```
