# Ovolvulus_RADseq


- having a play with some really old RADseq data I generated at LTU on the MiSeq
- new version of STACKs (v2) and a new manuscript describing best use of STACKs
(https://www.biorxiv.org/content/10.1101/2021.11.02.466953v1.full.pdf) so thought I'd give it a go.



## Project setup
```bash
WORKING_DIR=/nfs/users/nfs_s/sd21/lustre118_link/ov_ltu/RADSEQ_2021

cd ${WORKING_DIR}

mkdir 00_SCRIPTS 01_REF 02_RAW

# will add more as I go.

```


## Reference setup
```
WORKING_DIR=/nfs/users/nfs_s/sd21/lustre118_link/ov_ltu/RADSEQ_2021

cd ${WORKING_DIR}/01_REF

wget https://ftp.ebi.ac.uk/pub/databases/wormbase/parasite/releases/WBPS16/species/onchocerca_volvulus/PRJEB513/onchocerca_volvulus.PRJEB513.WBPS16.genomic.fa.gz

bwa index onchocerca_volvulus.PRJEB513.WBPS16.genomic.fa.gz


## Raw data
- the raw data has already been demuliplexed ages ago, so just going to use that.
- 181 samples, labelled by Illumina index (eg. IDX01) and RAD tag (eg. RAD01)


```bash
WORKING_DIR=/nfs/users/nfs_s/sd21/lustre118_link/ov_ltu/RADSEQ_2021

cd ${WORKING_DIR}/01_REF

cp ../../RADseq_2017/DEMULTIPLEX/NEW/*.fq .

```




## Mapping
- using simple bwa mem script to run mapping, as per the paper.

```bash
WORKING_DIR=/nfs/users/nfs_s/sd21/lustre118_link/ov_ltu/RADSEQ_2021

cd ${WORKING_DIR}/00_SCRIPTS

bsub.py --queue yesterday 10 map "./run_mapping.sh"

```

where "run_mapping.sh" is:
```bash
#!/bin/bash

for i in `ls -1 ${WORKING_DIR}/02_RAW/*.1.fq | xargs -n1 basename | sed 's/.1.fq//g' `; do
bwa mem ${WORKING_DIR}/01_REF/onchocerca_volvulus.PRJEB513.WBPS16.genomic.fa.gz \
 ${WORKING_DIR}/02_RAW/${i}.1.fq \
 ${WORKING_DIR}/02_RAW/${i}.2.fq | \
 samtools view -b -h | \
 samtools sort \
 -o ${WORKING_DIR}/03_MAPPING/${i}.bam;
done
```


## Run Ref Map
- this is the main stacks pipeline to compare RAD tags between different samples
- using the wrapper script "ref_map.pl" which is for data mapped to a reference genome as has been done here
- wrapper needs to point to the mapped bams from the previous step, and a "population map", which I generated way back.
     - the population map is a tab delimited file, containing a minimum two columns, "sample ID" and "population". note, the file doesn't have a header
     - can have a third column, as included here, containing addition populaiton data.
     - Eg. samples.popmap2

| IDX01_RAD02	| AB1 | 	black_volta |
| IDX01_RAD03	| AB1 |	black_volta |
| IDX01_RAD04	| AB1 |	black_volta |
| IDX01_RAD05	| AB1 |	black_volta |
| IDX01_RAD12	| AB1 |	black_volta |
| IDX01_RAD14	| AB1 |	black_volta |
| IDX01_RAD17	| AB1 |	black_volta |
| IDX01_RAD18	| AB1 |	black_volta |
| IDX01_RAD24	| AB2 |	black_volta |
| IDX01_RAD26	| AB2 |	black_volta |

```bash
WORKING_DIR=/nfs/users/nfs_s/sd21/lustre118_link/ov_ltu/RADSEQ_2021

mkdir 04_REFMAP/INFO

cp ../RADseq_2017/DEMULTIPLEX/samples.popmap* 04_REFMAP/INFO/


cd ${WORKING_DIR}/00_SCRIPTS

bsub.py --queue yesterday 10 refmap "./run_ref-map.sh"

```


where "run_ref-map.sh" is:
```bash
#!/bin/bash

WORKING_DIR=/nfs/users/nfs_s/sd21/lustre118_link/ov_ltu/RADSEQ_2021

ref_map.pl \
--samples ${WORKING_DIR}/03_MAPPING \
 --popmap ${WORKING_DIR}/04_REFMAP/INFO/samples.popmap2 \
 --out-path ${WORKING_DIR}/04_REFMAP \
 --rm-pcr-duplicates

 ```
