PacmonsTR
=========

#### Tandem Repeat Detection for Long Read Sequence Data

The pipeline works in the following steps:

1. The initial run takes in an alignment file (alignment of long reads with the reference, such as, hg19) and a reference Tandem Repeat table as INPUT and generates an OUTPUT file with estimated tanderm repeat region in the Long read, expected value of the number of tandem repeat elements, estimated Structural variation region in the tandem repeat.

2. The output file generated from Step A, is used (after processing) for clustering. The clustering uses GMM based clustering method, which uses AIC as the model selection criterion, coupled with c-separation. Allelic call (homozygous/heterozyous) is made based on the clustering criteria and it outputs two files. One file has all the calls and the other has the read data for each cluster.

3. The read data for each cluster is taken and a consensus sequence is generated. 

4. The consensus sequence can be taken as an input to StepA and re-estimation of TR multiplicity can be performed.


Requirements
-------------

PacmonSTR has the following dependencies:

1. Blasr: PacmonSTR assumes input alignment information about the long reads versus the reference in .m5 format generated by Blasr. Blasr is a long read aligner by PacBioScience and can be installed from https://github.com/PacificBiosciences/blasr.

2. Cython: The dynamic programming and pairHMM routines are written in cython. If the users wants to make any changes to the source code, then Cython is required to complie the code. Please check: http://docs.cython.org/src/quickstart/install.html for information on detailed installation steps.

3. Scikit-learn: sklearn's mixture module has been used for the TR allele calling functionality. Please note that sklearn depends on C compiler, scipy & numpy, among other modules. Please check: http://scikit-learn.org/stable/install.html for information on detailed installation steps.

Usage
-----

1. For running the pipeline, please run the following:
python $path/pacMonStr_V1.py $path/test.m5 $path/test.binned_anchors $flank $numCore $runDir

where,
a) path="The location of PacmonSTR directory"

b) $path/test.m5 = The input alignment between the long reads data and the reference. Here .m5 format which is an output of 'Blasr' tool is assumed.

c) $path/test.binned_anchors = The long reads (based on their alignement start and end positions with respect to the reference) that span the TR event (defined for each chromosome by their start position, end position, repeat element, repeat period, repeat multiplicity, among other attributes) are written in this file with their flanking distance upstream and downstream of the TR event.

d) $flank = The flanking distance upstream and downstream of the TR event. They are assumed to be same.

e) $numCore = Number of processors to be used. The pipeline uses multiprocessing library (which is in standard python) for spawning processes via pool.

f) $runDir = The directory where the output files should be generated. Please note that using multiple processors results in writing of many out files and this can be controlled by modifying the value of 'num' variable in the source code. Right now it uses a factor of 1.0/0.05. This parameter is used to partition the dictionary queryAligns, and passes that partition to a processor via pool. Higher the number lower the number of partitions created.

2. For running the clustering part of the pipeline, please run 'source runClustering.sh'. This script processes the output from the previous step and uses that as an input for the gmmCluster/clusteringGMM_AIC_cSep.py. The input parameters for clustering are specified in the script.


