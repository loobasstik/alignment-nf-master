alignment-nf
============

Nextflow pipeline to perform BAM realignment or fastq alignment,
with/without local indel realignment and base quality score
recalibration.

Overview of pipeline workflow
-----------------------------

.. figure:: WESpipeline.png?raw=true
   :alt: Scheme of alignment/realignment Workflow

   workflow

Prerequisites
-------------

For the **basic fastq files alignment** without indel realignment, base
quality score recalibration, nor alternative contif handling, the script
requires the following programs: -
`*nextflow* <http://www.nextflow.io/>`__. Install with
``bash     curl -fsSL get.nextflow.io | bash`` And move it to a location
in your ``$PATH`` (e.g., ``/usr/local/bin``):
``bash     sudo mv nextflow /usr/local/bin`` -
`*bwa* <https://github.com/lh3/bwa>`__ -
`*samblaster* <https://github.com/GregoryFaust/samblaster>`__ -
`*sambamba* <https://github.com/lomereiter/sambamba>`__ and the
following files: - a fasta reference genome with its index files
(*.fai*, *.sa*, *.bwt*, *.ann*, *.amb*, *.pac*; in the same directory) -
a folder with fastq files

In addition, for the **bam files realignment**: -
`*samtools* <http://samtools.sourceforge.net/>`__ - a folder with bam
files

For the **adapter sequence trimming**: -
`*AdapterRemoval* <https://github.com/MikkelSchubert/adapterremoval>`__

For the **ALT contigs handling**, additional softwares and scripts are
required: - the *k8* javascript execution shell (e.g., available in the
`*bwakit* <https://sourceforge.net/projects/bio-bwa/files/bwakit/>`__
archive) - javascript bwa-postalt.js and the additional fasta reference
*.alt* file from
`*bwakit* <https://github.com/lh3/bwa/tree/master/bwakit>`__ must be in
the same directory as the reference genome file.

For the **indel realignment**: - GATK
`*GenomeAnalysisTK.jar* <https://software.broadinstitute.org/gatk/guide/quickstart>`__
- `GATK
bundle <https://software.broadinstitute.org/gatk/download/bundle>`__ VCF
files with lists of indels (recommended: 1000 genomes and Mills gold
standard VCFs)

For the **base quality score recalibration**: - GATK
`*GenomeAnalysisTK.jar* <https://software.broadinstitute.org/gatk/guide/quickstart>`__
- `GATK
bundle <https://software.broadinstitute.org/gatk/download/bundle>`__ VCF
files with lists of indels and SNVs (recommended: 1000 genomes indels,
Mills gold standard indels VCFs, dbsnp VCF) - bed file with intervals to
be considered

Usage
-----

.. code:: bash

    nextflow run iarcbioinfo/alignment-nf --input_folder input --fasta_ref hg19.fasta --out_folder output

Enable adapter trimming
~~~~~~~~~~~~~~~~~~~~~~~

To use the adapter trimming step, you must add the ***--trim* option**,
as well as satisfy the requirements above mentionned. For example:

.. code:: bash

    nextflow run iarcbioinfo/alignment-nf --input_folder input --fasta_ref reference/hs38DH.fa -out_folder output --trim

Enable ALT mode
~~~~~~~~~~~~~~~

To use the alternative contigs handling mode, you must provide the
**path to an ALT aware genome reference** (e.g., hg38) AND add the
***--alt* option**, as well as satisfy the requirements above
mentionned. For example:

.. code:: bash

    nextflow run iarcbioinfo/alignment-nf --input_folder input --fasta_ref reference/hs38DH.fa --js /user/bin/k8/k8 --postaltjs /user/bin/bwa-0.7.15/bwakit/bwa-postalt.js -out_folder output --alt

Enable local indel realignment
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

To use the local indel realignment step, you must provide the **path to
the GATK jar file**, the **GATK bundle folder**, AND add the
***--indel\_realignment* option**, as well as satisfy the requirements
above mentionned. For example:

.. code:: bash

    nextflow run iarcbioinfo/alignment-nf --GATK_bundle GATKbundle/hg19 --input_folder input --fasta_ref reference/hg19.fa --GATK_folder /user/bin7GATK-3.6-0 --out_folder output --indel_realignment

Enable base quality score recalibration
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

To use the base quality score recalibration step, you must provide the
**path to the GATK jar file**, the **GATK bundle folder**, a **bed
file**, AND add the ***--recalibration* option**, as well as satisfy the
requirements above mentionned. For example:

.. code:: bash

    nextflow run iarcbioinfo/alignment-nf --GATK_bundle GATKbundle/hg19 --input_folder input --fasta_ref reference/hg19.fa --GATK_folder /user/bin7GATK-3.6-0 --intervals reference/hg19_intervals.bed --out_folder output --recalibration

All parameters
--------------

+--------------+------------------+----------------+
| **PARAMETER* | **DEFAULT**      | **DESCRIPTION* |
| *            |                  | *              |
+==============+==================+================+
| *--help*     | null             | print usage    |
|              |                  | and optional   |
|              |                  | parameters     |
+--------------+------------------+----------------+
| *--input\_fo | .                | input folder   |
| lder*        |                  |                |
+--------------+------------------+----------------+
| *--fasta\_re | hg19.fasta       | genome         |
| f*           |                  | reference      |
+--------------+------------------+----------------+
| *--cpu*      | 8                | number of CPUs |
+--------------+------------------+----------------+
| *--mem*      | 32               | memory         |
+--------------+------------------+----------------+
| *--mem\_samb | 1                | memory for     |
| amba*        |                  | software       |
|              |                  | *sambamba*     |
+--------------+------------------+----------------+
| *--RG*       | PL:ILLUMINA      | sequencing     |
|              |                  | information    |
|              |                  | for aligned    |
|              |                  | (for *bwa*)    |
+--------------+------------------+----------------+
| *--fastq\_ex | fastq.gz         | extension of   |
| t*           |                  | fastq files    |
+--------------+------------------+----------------+
| *--suffix1*  | \_1              | suffix for     |
|              |                  | second element |
|              |                  | of read files  |
|              |                  | pair           |
+--------------+------------------+----------------+
| *--suffix2*  | \_2              | suffix for     |
|              |                  | second element |
|              |                  | of read files  |
|              |                  | pair           |
+--------------+------------------+----------------+
| *--out\_fold | .                | output folder  |
| er*          |                  | for aligned    |
|              |                  | BAMs           |
+--------------+------------------+----------------+
| *--intervals |                  | bed file with  |
| *            |                  | interval list  |
+--------------+------------------+----------------+
| *--GATK\_bun | bundle           | path to GATK   |
| dle*         |                  | bundle files   |
+--------------+------------------+----------------+
| *--GATK\_fol | .                | path to GATK   |
| der*         |                  | *GenomeAnalysi |
|              |                  | sTK.jar*       |
|              |                  | file           |
+--------------+------------------+----------------+
| *--trim*     | false            | enable adapter |
|              |                  | sequence       |
|              |                  | trimming       |
+--------------+------------------+----------------+
| *--indel\_re | false            | perform local  |
| alignment*   |                  | indel          |
|              |                  | realignment    |
|              |                  | (GATK)         |
+--------------+------------------+----------------+
| *--recalibra | false            | perform        |
| tion*        |                  | quality score  |
|              |                  | recalibration  |
|              |                  | (GATK)         |
+--------------+------------------+----------------+
| *--js*       | k8               | path to        |
|              |                  | javascript     |
|              |                  | interpreter    |
|              |                  | *k8*           |
+--------------+------------------+----------------+
| *--postaltjs | bwa-postalt.js"  | path to        |
| *            |                  | postalignment  |
|              |                  | javascript     |
|              |                  | *bwa-postalt.j |
|              |                  | s*             |
+--------------+------------------+----------------+
| *--alt*      | false            | enable         |
|              |                  | alternative    |
|              |                  | contig         |
|              |                  | handling (for  |
|              |                  | reference      |
|              |                  | genome hg38)   |
+--------------+------------------+----------------+
