# Questions for the ENCODE Data Wrangler Test.

*Instructions:  These tests are to express the variety of tasks you would perform in this job.  Please choose 4 of the 5 assignments.*

## Exercise 1
Pick any scientific paper.  Write a short paragraph (8-10 sentences) in your own words describing the key experiments and conclusions from that publication.  Include a PDF of the paper you chose when you submit your exercises.


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


## Exercise 3
Read the directions in the 'pileup-script-drections' document.  The 'BL6_x_Cast_RNA.pileup' contains the data to be analyzed.  Please provide your script and the results file.


## Exercise 4
The ENCODE Portal is based on a REST API.  This means that the information that the web page uses to create webpages is the same information you would get as programmatically querying the ENCODE database.  Any URL that is generated when you click on the Portal can be programmatically queried to get the information back in a parsable format.  An introduction to the ENCODE Portal and the REST API are described in these help documents:

* Getting Started: https://www.encodeproject.org/help/getting-started
* The ENCODE REST API: https://www.encodeproject.org/help/rest-api/

For this exercise, write a script that queries the ENCODE database and gives a tab-delimited results file for the following query:

> Give me the experiment accession, biosample, protein target, and number of replicates for all ChIP-seq assays released in August of 2012.

Please submit your script and results file.


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