# Questions for the ENCODE Data Wrangler Test

*Instructions:  These tests are to express the variety of tasks you would perform in this job.  Please choose 4 of the 5 assignments.*

## Exercise 1
Pick any scientific paper.  Write a short paragraph (8-10 sentences) in your own words describing the key experiments and conclusions from that publication.  Include a PDF of the paper you chose when you submit your exercises.

**[Answer](https://github.com/keenangraham/encode_data_wrangler_test/tree/master/answers/question_001)**

**[PDF](https://github.com/keenangraham/encode_data_wrangler_test/blob/master/answers/question_001/eid_research_paper.pdf)**


## Exercise 2
Read the following publication and its associated references.

Primary publication:

- http://www.ncbi.nlm.nih.gov/pubmed/19814794

Related references:

- http://www.ncbi.nlm.nih.gov/pubmed/17873876
- http://www.ncbi.nlm.nih.gov/pubmed/17392789
- http://www.ncbi.nlm.nih.gov/pubmed/19092803

Answer these questions in 1-2 pages:

* Where could we obtain each dataset?
* What would you do to compare results from these datasets?
* What types of errors are possible in these datasets?
* What validation we would need to perform on the data?  In other words, if you were a user of SGD and retrieved these data what would you assume was validated?
* What data would we make available?
* How could we display the individual datasets and the Pugh results?
* Where do you see issues and connections between these data and information already in SGD or other biological data?

**Skipped (no answer)**


## Exercise 3
Read the directions below.  The 'BL6_x_Cast_RNA.pileup' file in the data folder contains the data to be analyzed.  Please provide your script and the results file.

**Background:** 

In pileup format, each line represents one nucleotide of the reference sequence, so for the mouse genome, a whole-genome pileup file will be over 2.7 billion lines.  This is too much data to deal with for a problem set, so we’ve given you a subset of chromosome 7 that has less than 5 thousand lines. The format of the pileup file is tab-delimited, with the following fields:

| No. | Field |
| ---- | ---- |
| **1** | Reference chromosome |
| **2** | Reference position (1-indexed) |
| **3** | Reference base (“forward” strand) |
| **4** | Sequence coverage |
| **5** | Sequence pile | 
| **6** | Sequence base qualities |

The 5th field, for which this format is named, has a single character for every read that mapped to the reference genome at this position.  Additionally, the pile shows whether a base is the first or last base in a read, as well as whether insertions/deletions (indels) span the reference base.  Here is an example line from a pileup file:

```
chr1	50	C	34	.$.$.$.$T,..A...,,,....+4AGGC.........,a,^F,^].^F,	[[`p!S__^\\\a`ZU`X\B_Va^___`^[_a\W
```

Below is a table showing the valid characters in a pile, as well regular expressions one could use to extract the given characters from the pile. For indels, the below lengths are just examples. Indels can be of any length.

| Character(s) | Meaning | Suggested Regular Expression	|
| ------------ | ------- | ---------------------------- |
| . | Match to reference base on forward strand | /\./ |
| , | Match to reference base on reverse strand | /,/ |
| ACGT | Mismatch on forward strand | /[ACGT]/ |
| acgt | Mismatch on reverse strand | /[acgt]/ |
| ^x | Start of a read, where x is the mapping quality as ASCII character - 33 (see below); precedes the character that indicates the nucleotide at that position from the read | /\^./ |
| $ | End of read; follows the character that indicates the nucleotide at that position from the read | /\$/ |	
| +2AA | Insertion of two bases relative to the reference. ‘+’ indicates an insertion, ‘2’ means it is 2 bases in length, ‘AA’ means that two ‘A’ bases are inserted. | /\+\d+[ACGT]+/ |
| -3ACG | Deletion of three bases relative to the reference. Same conventions as insertions, except a ‘-‘ signifying a deletion. | /\-\d+[ACGT]+/ |


For the example line above, the reads map to chromosome 1, position 50. The reference base is a C and the sequencing coverage at this position is 34x. There are 4 reads that end at this position (represented by '$'), and there are 3 reads that start at this position (represented by '^x', where 'x' is an ASCII character - 33, which indicates that read's mapping quality - in this case ‘^F’, ‘^]’, and ‘^F’).  There is also one 4-base insertion relative to the reference (+4AGGC).  There are 2 reads that have an 'A' at this position, and one read that has a 'T'. Finally, there are 31 reads that have a 'C' at this position, the same as the reference base, so they are represented by either a dot or comma, depending on strand. Notice that only dots, commas, and 'ACGTacgt' characters not involved in mapping quality or indels contribute to overall coverage. This is because the read start (^x) and read end ($) indicators only serve as landmarks and do not contain sequence information. Indels also do not contribute to coverage since they are not present in the reference genome.

The 6th field contains the base qualities.  There is one base quality character for each character contributing to coverage in the pile, so in the example above, there are 34 base quality characters. The conversion is the same as with mapping qualities, where the integer mapping quality is calculated by taking the ASCII value of the character minus 33.  In Perl and Python, this can be done using the ord() function:

```python
quality = ord("P") - 33 # 47 in decimal
````

**Challenge:**

Write a script in Perl or Python that parses the pileup file line-by-line, and will call a consensus base using two criteria:

1. Sequencing coverage

2. Proportion of consensus mismatches relative to the reference.

Call a consensus base if:

1. The sequencing coverage is greater than or equal to 5X, but less than 100X, and

2. The percentage of consensus mismatches relative to the reference is greater than or equal to 80% of the total number of bases in the pile.

However, **ignore bases whose sequencing quality score is below 40**.  That is, do not use them to compute either the proportion of consensus mismatches, or the total coverage.

**Methods:**

Consensus base calling from piles is partly an exercise in regular expressions.  Since the piles are riddled with mapping qualities that can contain 'ACGTacgt' following a carat (^), as well as indels, you must parse the pile carefully, taking care to **only count the characters indicating a mismatch, and not indicating mapping qualities or indels.**  That is, a mapping quality such as ‘^A’ that occurs in the pile should not be taken as an ‘A’ from sequencing, but rather seen as a mapping quality and ignored.  Similarly, indel characters such as ‘-5ATGCC’ or ‘+2AA’ should not contribute to coverage.

**Output:**

The output file should have the following format:

| Col. 1 | Col. 2 | Col. 3 |
| ------ | ------ | ------ |
| Reference chromosome | Reference position (1-indexed) | Calculated consensus base |

**[Answer](http://nbviewer.jupyter.org/github/keenangraham/encode_data_wrangler_test/blob/master/answers/question_003/pileup_base_caller.ipynb)**

**[Results File (TSV)](https://github.com/keenangraham/encode_data_wrangler_test/blob/master/answers/question_003/consensus_bases.tsv)**

<img src="https://raw.githubusercontent.com/keenangraham/encode_data_wrangler_test/master/answers/question_003/pileup_consensus.PNG"><br>
*Visualization of results*


## Exercise 4
The ENCODE Portal is based on a REST API.  This means that the information that the web page uses to create webpages is the same information you would get as programmatically querying the ENCODE database.  Any URL that is generated when you click on the Portal can be programmatically queried to get the information back in a parsable format.  An introduction to the ENCODE Portal and the REST API are described in these help documents:

* Getting Started: https://www.encodeproject.org/help/getting-started
* The ENCODE REST API: https://www.encodeproject.org/help/rest-api/

For this exercise, write a script that queries the ENCODE database and gives a tab-delimited results file for the following query:

> Give me the experiment accession, biosample, protein target, and number of replicates for all ChIP-seq assays released in August of 2012.

Please submit your script and results file.

**[Answer](http://nbviewer.jupyter.org/github/keenangraham/encode_data_wrangler_test/blob/master/answers/question_004/query_api.ipynb)**

**[Results File (TSV)](https://github.com/keenangraham/encode_data_wrangler_test/blob/master/answers/question_004/encode_results.tsv)**


## Exercise 5
Find data errors.

Some background:

*  The left arm of chromosomes in *Saccharomyces cerevisiae* are sequences left of the centromere.

* The genomic sequence (DNA) is annotated with different types of sequence features.  A sequence feature is what role that particular stretch of DNA plays - a protein-coding gene, a tRNA, a centromere.  Sequence features can be on the Watson strand or the Crick strand of the genome.

* An ORF is a sequence feature that is defined as an Open Reading Frame.  It is a gene that is translated into a protein.

You've downloaded a file called "SGDFeaturesChrXI.xlsx" that contains ORFs from the left arm of chromosome 11.  However, you notice that there are several errors in the file.  The first row, highlighted in yellow, contains correctly formatted data. The subsequent rows may contains data errors.  Can you find them and describe why each is wrong?

You should expect to find the following types of information in the columns:

| No. | Column name | Description | Required |
| ---- | ---- | ---- | ---- |
| **1** | Row number | Use this when you refer to the rows with errors | mandatory |
| **2** | Primary SGDID | Unique identifier for the sequence feature | mandatory |
| **3** | Feature type | Phrase that describes the sequence feature | mandatory |
| **4** | Feature name | Name that describes the approximate location of the sequence feature | optional |
| **5** | Gene name | Human-friendly name, following the pattern of 3 letters and a number | optional |
| **6** | Secondary SGDID | Alternate identifiers for the sequence feature | optional |
| **7** | Chromosome | Chromosome that the sequence feature is located on | optional | 
| **8** | Start coordinate | Coordinate number on the chromosome that contains the beginning of the start codon | mandatory |
| **9** | Stop coordinate | Coordinate number on the chromosome that contains the end of the stop codon | mandatory |
| **10** | Strand | Whether the ORF is on the Watson or Crick strand | mandatory |
| **11** | Sequence version date | Date when the genomic sequence was last modified | optional |
| **12** | Description | Short summary of the biological role of the ORF | optional |

**[Answer](http://nbviewer.jupyter.org/github/keenangraham/encode_data_wrangler_test/blob/master/answers/question_005/find_errors.ipynb)**

**[Results File (TSV)](https://github.com/keenangraham/encode_data_wrangler_test/blob/master/answers/question_005/error_results.tsv)**

