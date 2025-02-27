==============
**miComplete**
==============

- Eric Hugoson (eric@hugoson.org / hugoson@evolbio.mpg.de / @EricHugo)
- Lionel Guy (guy.lionel@gmail.com / lionel.guy@imbim.uu.se / @LionelGuy)
 

Introduction
----------------
With the increasing rate of the production of genomics data, particularly metagenomic data, there is need for more and faster quality control of resulting assembled genomes. An increasing usage of
metagenome assembled genomes (MAGs) means often working with incomplete genomes, which can be acceptable provided the researcher is aware of this. Therefore there is a clear use for software
able to rapidly and accurately provide the stats that describe the quality and completeness of the genomes or genomic bins of interest.

miComplete allows a user to provide a list of genomes or genomic bins to retrieve some basic statistics regarding the given genomes (size, GC-content, total number of CDS and contigs, as well as N- and L50, N- and L90). Further a set of marker genes
in HMM format can be provided to also retrieve completeness and redundancy of those markers in each genome. Additionally, a set of weights for the marker genes can be provided to also retrieve the
weighted versions of completeness and redundancy which can inform the user a bit more of the actual state completeness (see description). Alternatively, the user can calculate new weights for any given set
of marker genes provided.

miComplete is still in a state of development, bugs may be encountered. Feedback, bug reports, and feature requests are welcome through Bitbucket's
`issue system <https://bitbucket.org/evolegiolab/micomplete/issues>`_.

miComplete has been used in several publications:

#. [MartijnEtAl_2015]_
#. [SpangEtAl_2015]_
#. [MartijnEtAl_2018]_


Description
--------------
miComplete is a compact software aimed at rapidly and accurately determining of the quality of assembled genomes, often metagenome assembled bins. miComplete also aims at providing a more reliable completeness and redundancy
metric via a system of weighting the impact of different marker genes presence or absence differently.

Completeness
^^^^^^^^^^^^^^^
In miComplete completeness is calculated based on the presence/absence a set of marker genes provided as a set of HMMs. The presence or absence of the marker genes is determined by HMMER3 (see dependencies)
if the hit reported is below the cutoff e-value provided as well as passing the bias check. Redundancy is reported as the fraction duplicated markers of all markers. In similar software such as CheckM
this is reported as Contamination by percentage. E.g. 6% contamination in CheckM is equivalent to 1.06 redundancy in miComplete.

Weights
^^^^^^^^^^^
Because not all marker genes are equal in determining the completeness of a genome. Some genes associate closely together within a genome (particularly genes organised into operons), and thus viewing two genes that typically
associate together within an operon as providing the same completeness information as two unrelated genes could be profoundly misleading. miComplete is able to calculate a weighted version of completeness and redundancy
that attempts to factor in how closely the provided marker genes typically associate with other provided marker genes.

Linkage
"""""""""""""""""
Weights can be calculated for any given set of marker genes in miComplete. This is can be done by a user by providing a set of reference genomes (note that these need to be single contig chromosomes).
The reference genomes can be any set that the user wishes, but as general rule the larger and more diverse number of genomes the better weights. At the end of the run a violin plot of the distribution of
weights for all markers is produced along with a file of the relative weights to be used in future runs.


Dependencies
--------------
Python (>=3.5)


External software
^^^^^^^^^^^^^^^^^^^
Executables should be available in the user's ``$PATH``.

HMMER3
"""""""""""""""""
HMMER: biosequence analysis using profile hidden Markov models, by Sean Eddy and coworkers. Tested with v. 3.1b2. Available from <http://hmmer.org/>.

prodigal
""""""""""""""""
A gene prediction software by Doug Hyatt. Tested with v. 2.6.3. Download at:
<https://github.com/hyattpd/Prodigal>

Python libraries
^^^^^^^^^^^^^^^^^^^
If built from the package these will be installed automatically, otherwise can easily be installed using ``pip`` (`Install pip <https://pip.pypa.io/en/stable/installing/>`_).

Required
""""""""""""""""""

- Biopython (>= 1.70) (``$ pip install biopython``)
- Numpy (>= 1.13.1) (``$ pip install numpy``)
- Matplotlib (>= 2.0.2) (``$ pip install matplotlib``)

Note: *matplotlib* as implemented requires a user interface. By default it uses *Tkinter*, which can be installed via your systems package manager. Also note that there may be a specific Tkinter for Python 2 and 3 respectively, ensure that you install the one for Python3. If you need more help, consult the `TkDocs <https://tkdocs.com/tutorial/install.html>`_.
To instead alter backend used follow `these instructions <http://matplotlib.org/faq/usage_faq.html#what-is-a-backend>`_.


Installation
--------------

Python3
^^^^^^^
Most current distributions of GNU/Linux should have Python3.X installed natively. If your particular distribution does not have Python3 installed already, you should be able to install it
via your distrubion's package manager (e.g. apt/yum/zypper). If not, you should consider upgrading as Python2 is being sunset 2020, but if you cannot then you can still download any version
from Python's website: `here <https://www.python.org/downloads/>`_.

Virtual environment
^^^^^^^^^^^^^^^^^^^

To avoid breaking system services or other projects which may depend on a specific version of some packages it can be a good idea to install miComplete and its dependencies in a virtual environment. Initialise using python builtin venv::

    $ python3 -m venv micomplete
    $ source micomplete/bin/activate

Now the virtual environment is activated, and miComplete along with all its dependencies can safely be installed via pip from within the virtual environment, as shown below.

Python package
^^^^^^^^^^^^^^^^^^^

miComplete can easily be installed along with all python dependencies::

   $ pip install micomplete

Assuming that the python bin is in your ``$PATH``, can then be run as::

   $ miComplete

Git
^^^^^^^^^^^^^^^^^^^

1. Choose an appropriate location, e.g. your home::

   $ cd $HOME

2. Clone the latest version of the repository::

   $ git clone http://bitbucket.org/evolegiolab/micomplete.git

3. Create symlink to some directory in your ``$PATH`` (in this example ``$HOME/bin``)::

   $ cd micomplete
   $ ls micomplete
   $ ln -s $(realpath micomplete/micomplete.py) $HOME/bin/miComplete

4. Optionally, add the folder ``micomplete`` in your ``PATH``. The scripts should be kept at their original location.

Usage
--------------

Positional arguments
^^^^^^^^^^^^^^^^^^^^^^^
::

   A file of sequence(s) along with type (fna, faa, gbk) provided in a tabular format

The file has to contain per line both a path (relative or absolute) to a genomic file as well as the type separated by a tab. Optionally it can also be given a custom name separately from the filename in a third column::

   /seq/genomic_sources/legionella_pneumophila.gbk   gbk
   /seq/genomic_sources/coxiella_burnetii.fna fna
   /seq/genomic_sources/e_coli.fna   fna    MG1655_reference
   (...)

Optional arguments
^^^^^^^^^^^^^^^^^^^^^^^^

   -h, --help          show help message and exit
   --format FORMAT
                       Options: fna|faa|gbk. This argument should be used when a single sequence file is given in place of tabulated file of sequences. The argument should be followed by the format of the sequence.
   --hlist             Write list of Present, Absent and Duplicated markers for each organism to file
   --hmms HMMS         Specifies a set of HMMs to be used for completeness check or linkage analysis. The default sets, "Bact105" and "Arch131", can be called via their respective names.
   --weights WEIGHTS   Specify a set of weights for the HMMs specified. The default sets, "Bact105" and "Arch131", can be called via their respective names.
   --linkage           Specifies that the provided sequences should be used to calculate the weights of the provided HMMs
   --lenient           By default miComplete drops hits with too high bias or too low best domain score. This argument disables that behaviour, permitting any hit that meets the evalue requirements.
   --linkage-cutoff    Cutoff fraction of the entire fasta which needs to be contained in a single contig in order to be included in linkage calculations. Disabling this is likely to result in some erroneous calculations.
   --evalue EVALUE     Specify e-value cutoff to be used for completeness check. Default = 4e-10
   --bias BIAS         Specify the bias cutoff as a fraction of score defined by hammer. Default = 0.3
   --domain-cutoff     Specify the largest allowed difference between best domain evalue and protein evalue. Default = 1e-5
   --cutoff CUTOFF     Specify cutoff percentage of markers required to be present in genome for it be included in linkage calculation. Default = 0.9
   --threads THREADS   Specify number of threads to be used in parallel. Default = 1
   --log LOG           Log name. Default = miComplete.log
   -v, --verbose       Enable verbose logging.
   --debug             Debug logging.
   --version           Returns miComplete version and exits
   -o, --outfile OUTFILE    Name of outfile can be specified with this argument. By default prints to stdout.

Examples
^^^^^^^^^^^^^^^^^^^^^^^^
In a folder containing one or several FASTA files with '.fna' extensions, create a sequence tab file. Here it is best to avoid relative paths unless you know you will be running miComplete from the same relative directory. A correctly formatted input tab file can be created by hand or using a small utility script included with miComplete::

   $ find $(realpath .) -maxdepth 1 -type f -name "*.fna" | miCompletelist.sh > test_set.tab

miComplete_list.sh automatically detects valid extensions and adds the extension to the second column of the file, as miComplete expects. Note: .fasta and .fa are not considered valid extension as it is ambiguous whether they contain nucleotide- or amino acid sequence. Convert to .fna or .faa as appropriate.

Sequence tab file, test_set.tab::

   $ cat test_set.tab
    /seq/genomic_sources/legionella_longbeachae.fna  fna
    /seq/genomic_sources/coxiella_burnetii.fna   fna
    /seq/genomic_sources/coxiella-like_endosymbiont.fna  fna

Example 1 - Basic stats
""""""""""""""""""""""""

This example merely produces basic information about the given sequences::

   $ miComplete test_set.tab
    ## miComplete
    ## v1.1.0
    Name	Length	GC-content	Contigs	CDS	N50	L50	N90	L90	
    legionella_longbeachae	4149158	37.13	2	3549	4077332	1	4077332	1	
    coxiella_burnetii	2032807	42.6	2	2058	1995488	1	1995488	1	
    coxiella-like_endosymbiont	1733840	38.17	1	1968	1733840	1	1733840	1
   
miComplete prints result to stdout in tabular format, this can favourably be redirected towards a file with a pipe and examined with spreadsheet reader. ::

   $ miComplete test_set.tab > results.tab

Alternatively, if we only have a single genome/genomic bin to investigate there is no need to create a sequence_tab file, as long as we provide the ``--format`` argument to inform miComplete of what file format to expect::

   $ miComplete legionella_longbeachae.fna --format fna
    ## miComplete
    ## v1.1.0
    Name	Length	GC-content	Contigs	CDS	N50	L50	N90	L90	
    legionella_longbeachae	4149158	37.13	2	3549	4077332	1	4077332	1

This way of investigating a single genome is compatible with all subsequent examples' options.

Example 2 - Completeness
""""""""""""""""""""""""

This example will produce the same basic statistics, but also completeness and redundancy::

   $ miComplete test_set.tab --hmms Bact105
    ## miComplete
    ## v1.1.0
    Name	Length	GC-content	Present Markers	Completeness	Redundancy	Contigs	CDS	N50	L50	N90	L90	
    legionella_longbeachae	4149158	37.13	105	1.0000	1.0095	2	3549	4077332	1	4077332	1	
    coxiella_burnetii	2032807	42.6	105	1.0000	1.0000	2	2058	1995488	1	1995488	1	
    coxiella-like_endosymbiont	1733840	38.17	102	0.9714	1.0686	1	1968	1733840	1	1733840	1

   
That is great, but the run time is starting to increase significantly primarily due to needing to translate three genomes to proteomes.
We can speed up the process by running all three parallel with ``--threads 3``::

   $ miComplete test_set.tab --hmms Bact105 --threads 3 > results.tab
   
Example 3 - Weighted completeness
""""""""""""""""""""""""""""""""""

This example will also produce the weighted completeness::

   $ miComplete test_set.tab --hmms Bact105 --weights Bact105
    ## miComplete
    ## v1.1.0
    ## Weights:	/home/hugoson/.local/lib/python3.7/site-packages/micomplete/share/Bact105.weights
    ## Weights Standard deviation:	0.13917826966028532
    Name	Length	GC-content	Present Markers	Completeness	Redundancy	Weighted completeness	Weighted redundancy	Contigs	CDS	N50	L50	N90	L90	
    legionella_longbeachae	4149158	37.13	105	1.0000	1.0095	1.0	1.0151	2	3549	4077332	1	4077332	1	
    coxiella_burnetii	2032807	42.6	105	1.0000	1.0000	1.0	1.0	2	2058	1995488	1	1995488	1	
    coxiella-like_endosymbiont	1733840	38.17	102	0.9714	1.0686	0.9476	1.0855	1	1968	1733840	1	1733840	1

Example 4 - Creating weights
""""""""""""""""""""""""""""

Finally we will create our own set of weights given a set of marker genes for which we do not already have weights. In this example only three bacteria from the same order are used to create weights. Generally one should create weights with as a large number of well distributed (or at least as widely distributed as the data you intend to use the weights for) genomes::

   $ miComplete test_set.tab --hmms Bact105 --linkage --threads 4 > Bact105.weights

Also produces a violin plot of the distribution of weights for each marker gene.

References
----------------

.. [MartijnEtAl_2015] Martijn J, Schulz F, Zaremba-Niedzwiedzka K, Viklund J, Stepanauskas R, Andersson SG, Horn M, Guy L, Ettema TJG. Single-cell genomics of a rare environmental alphaproteobacterium provides unique insights into Rickettsiaceae evolution. ISME J. 2015 Nov;9(11):2373-85. doi: https://doi.org/10.1038/ismej.2015.46

.. [SpangEtAl_2015] Spang A, Saw JH, Jørgensen SL, Zaremba-Niedzwiedzka K, Martijn J, Lind AE, van Eijk R, Schleper C, Guy L, Ettema TJG. Complex archaea that bridge the gap between prokaryotes and eukaryote. Nature. 2015 May 14;521(7551):173-179. doi: https://doi.org/10.1038/nature14447

.. [MartijnEtAl_2018] Martijn J, Vosseberg J, Guy L, Offre P, Ettema TJG. Deep mitochondrial origin outside the sampled alphaproteobacteria. Nature. 2018 May;557(7703):101-105. doi: https://doi.org/10.1038/s41586-018-0059-5
	       
		
