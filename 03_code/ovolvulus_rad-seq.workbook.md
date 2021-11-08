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
```

## Raw data
- the raw data has already been demuliplexed ages ago, so just going to use that.
- 181 samples, labelled by Illumina index (eg. IDX01) and RAD tag (eg. RAD01)

### original demultiplexing
```bash
for i in `(cd ../RAW/ && ls *_R1.fq | sed -e 's/_R1.fq//g')`; do \
mkdir ${i}_PR_OUT; \
/nfs/users/nfs_s/sd21/lustre118_link/software/RADSEQ/stacks-2.0Beta2/bin/process_radtags \
-1 /nfs/users/nfs_s/sd21/lustre118_link/ov_ltu/RADseq_2017/RAW/${i}_R1.fq \
-2 /nfs/users/nfs_s/sd21/lustre118_link/ov_ltu/RADseq_2017/RAW/${i}_R2.fq \
-b /nfs/users/nfs_s/sd21/lustre118_link/ov_ltu/RADseq_2017/DEMULTIPLEX/RADseq_barcodes.list \
-o /nfs/users/nfs_s/sd21/lustre118_link/ov_ltu/RADseq_2017/DEMULTIPLEX/${i}_PR_OUT \
--renz_1 ecoRI \
-r -t 200; done

mkdir FILTERED_ALL

for i in `(cd ../RAW/ && ls *_R1.fq | sed -e 's/_R1.fq//g')`; do \
./run_RAD_clean ${i}; done

cp *_PR_OUT/*.fq FILTERED_ALL/


cd FILTERED_ALL
nano rename.data # made file with index/barcode and actual oncho sample names, to rename the files with meaningful info
while read old new; do mv ${old}.2.fq ${new}_${old}.2.fq; done < rename.data
rm IDX*  # remove any remaining indexes for which there should not be any data
```
- where "run_RAD_clean" is:
```bash
#!/bin/bash

index=$1

cd ${index}_PR_OUT


mv sample_GCATG.1.fq "$index"_RAD01.1.fq
mv sample_AACCA.1.fq "$index"_RAD02.1.fq
mv sample_CGATC.1.fq "$index"_RAD03.1.fq
mv sample_TCGAT.1.fq "$index"_RAD04.1.fq
mv sample_TGCAT.1.fq "$index"_RAD05.1.fq
mv sample_CAACC.1.fq "$index"_RAD06.1.fq
mv sample_GGTTG.1.fq "$index"_RAD07.1.fq
mv sample_AAGGA.1.fq "$index"_RAD08.1.fq
mv sample_AGCTA.1.fq "$index"_RAD09.1.fq
mv sample_ACACA.1.fq "$index"_RAD10.1.fq
mv sample_AATTA.1.fq "$index"_RAD11.1.fq
mv sample_ACGGT.1.fq "$index"_RAD12.1.fq
mv sample_ACTGG.1.fq "$index"_RAD13.1.fq
mv sample_ACTTC.1.fq "$index"_RAD14.1.fq
mv sample_ATACG.1.fq "$index"_RAD15.1.fq
mv sample_ATGAG.1.fq "$index"_RAD16.1.fq
mv sample_ATTAC.1.fq "$index"_RAD17.1.fq
mv sample_CATAT.1.fq "$index"_RAD18.1.fq
mv sample_CGAAT.1.fq "$index"_RAD19.1.fq
mv sample_CGGCT.1.fq "$index"_RAD20.1.fq
mv sample_CGGTA.1.fq "$index"_RAD21.1.fq
mv sample_CGTAC.1.fq "$index"_RAD22.1.fq
mv sample_CGTCG.1.fq "$index"_RAD23.1.fq
mv sample_CTGAT.1.fq "$index"_RAD24.1.fq
mv sample_CTGCG.1.fq "$index"_RAD25.1.fq
mv sample_CTGTC.1.fq "$index"_RAD26.1.fq
mv sample_CTTGG.1.fq "$index"_RAD27.1.fq
mv sample_GACAC.1.fq "$index"_RAD28.1.fq
mv sample_GAGAT.1.fq "$index"_RAD29.1.fq
mv sample_GAGTC.1.fq "$index"_RAD30.1.fq
mv sample_GCCGT.1.fq "$index"_RAD31.1.fq
mv sample_GCTGA.1.fq "$index"_RAD32.1.fq
mv sample_GGATA.1.fq "$index"_RAD33.1.fq
mv sample_GGCCA.1.fq "$index"_RAD34.1.fq
mv sample_GGCTC.1.fq "$index"_RAD35.1.fq
mv sample_GTAGT.1.fq "$index"_RAD36.1.fq
mv sample_GTCCG.1.fq "$index"_RAD37.1.fq
mv sample_GTCGA.1.fq "$index"_RAD38.1.fq
mv sample_TACCG.1.fq "$index"_RAD39.1.fq
mv sample_TACGT.1.fq "$index"_RAD40.1.fq
mv sample_TAGTA.1.fq "$index"_RAD41.1.fq
mv sample_TATAC.1.fq "$index"_RAD42.1.fq
mv sample_TCACG.1.fq "$index"_RAD43.1.fq
mv sample_TCAGT.1.fq "$index"_RAD44.1.fq
mv sample_TCCGG.1.fq "$index"_RAD45.1.fq
mv sample_TCTGC.1.fq "$index"_RAD46.1.fq
mv sample_TGGAA.1.fq "$index"_RAD47.1.fq
mv sample_TTACC.1.fq "$index"_RAD48.1.fq
mv sample_GCATG.2.fq "$index"_RAD01.2.fq
mv sample_AACCA.2.fq "$index"_RAD02.2.fq
mv sample_CGATC.2.fq "$index"_RAD03.2.fq
mv sample_TCGAT.2.fq "$index"_RAD04.2.fq
mv sample_TGCAT.2.fq "$index"_RAD05.2.fq
mv sample_CAACC.2.fq "$index"_RAD06.2.fq
mv sample_GGTTG.2.fq "$index"_RAD07.2.fq
mv sample_AAGGA.2.fq "$index"_RAD08.2.fq
mv sample_AGCTA.2.fq "$index"_RAD09.2.fq
mv sample_ACACA.2.fq "$index"_RAD10.2.fq
mv sample_AATTA.2.fq "$index"_RAD11.2.fq
mv sample_ACGGT.2.fq "$index"_RAD12.2.fq
mv sample_ACTGG.2.fq "$index"_RAD13.2.fq
mv sample_ACTTC.2.fq "$index"_RAD14.2.fq
mv sample_ATACG.2.fq "$index"_RAD15.2.fq
mv sample_ATGAG.2.fq "$index"_RAD16.2.fq
mv sample_ATTAC.2.fq "$index"_RAD17.2.fq
mv sample_CATAT.2.fq "$index"_RAD18.2.fq
mv sample_CGAAT.2.fq "$index"_RAD19.2.fq
mv sample_CGGCT.2.fq "$index"_RAD20.2.fq
mv sample_CGGTA.2.fq "$index"_RAD21.2.fq
mv sample_CGTAC.2.fq "$index"_RAD22.2.fq
mv sample_CGTCG.2.fq "$index"_RAD23.2.fq
mv sample_CTGAT.2.fq "$index"_RAD24.2.fq
mv sample_CTGCG.2.fq "$index"_RAD25.2.fq
mv sample_CTGTC.2.fq "$index"_RAD26.2.fq
mv sample_CTTGG.2.fq "$index"_RAD27.2.fq
mv sample_GACAC.2.fq "$index"_RAD28.2.fq
mv sample_GAGAT.2.fq "$index"_RAD29.2.fq
mv sample_GAGTC.2.fq "$index"_RAD30.2.fq
mv sample_GCCGT.2.fq "$index"_RAD31.2.fq
mv sample_GCTGA.2.fq "$index"_RAD32.2.fq
mv sample_GGATA.2.fq "$index"_RAD33.2.fq
mv sample_GGCCA.2.fq "$index"_RAD34.2.fq
mv sample_GGCTC.2.fq "$index"_RAD35.2.fq
mv sample_GTAGT.2.fq "$index"_RAD36.2.fq
mv sample_GTCCG.2.fq "$index"_RAD37.2.fq
mv sample_GTCGA.2.fq "$index"_RAD38.2.fq
mv sample_TACCG.2.fq "$index"_RAD39.2.fq
mv sample_TACGT.2.fq "$index"_RAD40.2.fq
mv sample_TAGTA.2.fq "$index"_RAD41.2.fq
mv sample_TATAC.2.fq "$index"_RAD42.2.fq
mv sample_TCACG.2.fq "$index"_RAD43.2.fq
mv sample_TCAGT.2.fq "$index"_RAD44.2.fq
mv sample_TCCGG.2.fq "$index"_RAD45.2.fq
mv sample_TCTGC.2.fq "$index"_RAD46.2.fq
mv sample_TGGAA.2.fq "$index"_RAD47.2.fq
mv sample_TTACC.2.fq "$index"_RAD48.2.fq

rm sample*
rm *discards


for i in "$index"_RAD01.1.fq \
"$index"_RAD02.1.fq \
"$index"_RAD03.1.fq \
"$index"_RAD04.1.fq \
"$index"_RAD05.1.fq \
"$index"_RAD06.1.fq \
"$index"_RAD07.1.fq \
"$index"_RAD08.1.fq \
"$index"_RAD09.1.fq \
"$index"_RAD10.1.fq \
"$index"_RAD11.1.fq \
"$index"_RAD12.1.fq \
"$index"_RAD13.1.fq \
"$index"_RAD14.1.fq \
"$index"_RAD15.1.fq \
"$index"_RAD16.1.fq \
"$index"_RAD17.1.fq \
"$index"_RAD18.1.fq \
"$index"_RAD19.1.fq \
"$index"_RAD20.1.fq \
"$index"_RAD21.1.fq \
"$index"_RAD22.1.fq \
"$index"_RAD23.1.fq \
"$index"_RAD24.1.fq \
"$index"_RAD25.1.fq \
"$index"_RAD26.1.fq \
"$index"_RAD27.1.fq \
"$index"_RAD28.1.fq \
"$index"_RAD29.1.fq \
"$index"_RAD30.1.fq \
"$index"_RAD31.1.fq \
"$index"_RAD32.1.fq \
"$index"_RAD33.1.fq \
"$index"_RAD34.1.fq \
"$index"_RAD35.1.fq \
"$index"_RAD36.1.fq \
"$index"_RAD37.1.fq \
"$index"_RAD38.1.fq \
"$index"_RAD39.1.fq \
"$index"_RAD40.1.fq \
"$index"_RAD41.1.fq \
"$index"_RAD42.1.fq \
"$index"_RAD43.1.fq \
"$index"_RAD44.1.fq \
"$index"_RAD45.1.fq \
"$index"_RAD46.1.fq \
"$index"_RAD47.1.fq \
"$index"_RAD48.1.fq \
"$index"_RAD01.2.fq \
"$index"_RAD02.2.fq \
"$index"_RAD03.2.fq \
"$index"_RAD04.2.fq \
"$index"_RAD05.2.fq \
"$index"_RAD06.2.fq \
"$index"_RAD07.2.fq \
"$index"_RAD08.2.fq \
"$index"_RAD09.2.fq \
"$index"_RAD10.2.fq \
"$index"_RAD11.2.fq \
"$index"_RAD12.2.fq \
"$index"_RAD13.2.fq \
"$index"_RAD14.2.fq \
"$index"_RAD15.2.fq \
"$index"_RAD16.2.fq \
"$index"_RAD17.2.fq \
"$index"_RAD18.2.fq \
"$index"_RAD19.2.fq \
"$index"_RAD20.2.fq \
"$index"_RAD21.2.fq \
"$index"_RAD22.2.fq \
"$index"_RAD23.2.fq \
"$index"_RAD24.2.fq \
"$index"_RAD25.2.fq \
"$index"_RAD26.2.fq \
"$index"_RAD27.2.fq \
"$index"_RAD28.2.fq \
"$index"_RAD29.2.fq \
"$index"_RAD30.2.fq \
"$index"_RAD31.2.fq \
"$index"_RAD32.2.fq \
"$index"_RAD33.2.fq \
"$index"_RAD34.2.fq \
"$index"_RAD35.2.fq \
"$index"_RAD36.2.fq \
"$index"_RAD37.2.fq \
"$index"_RAD38.2.fq \
"$index"_RAD39.2.fq \
"$index"_RAD40.2.fq \
"$index"_RAD41.2.fq \
"$index"_RAD42.2.fq \
"$index"_RAD43.2.fq \
"$index"_RAD44.2.fq \
"$index"_RAD45.2.fq \
"$index"_RAD46.2.fq \
"$index"_RAD47.2.fq \
"$index"_RAD48.2.fq
do

count=`wc -l $i | awk '{ print $1}'`
if [ $count -lt 50000 ] ; then rm $i ;fi

done

cd ..
```

### Copy of old data to new directory
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
     - Eg. samples.popmap2 - 181 files in 14 populations and 4 groups.



     | IDX01_RAD02 	| AB1 	| black_volta 	|
     |:-----------:	|:---:	|:-----------:	|
     | IDX01_RAD03 	| AB1 	| black_volta 	|
     | IDX01_RAD04 	| AB1 	| black_volta 	|
     | IDX01_RAD05 	| AB1 	| black_volta 	|
     | IDX01_RAD12 	| AB1 	| black_volta 	|
     | IDX01_RAD14 	| AB1 	| black_volta 	|
     | IDX01_RAD17 	| AB1 	| black_volta 	|
     | IDX01_RAD18 	| AB1 	| black_volta 	|
     | IDX01_RAD24 	| AB2 	| black_volta 	|
     | IDX01_RAD26 	| AB2 	| black_volta 	|
     ... and so on.

- placeholder - neat way to generate markdown tables: https://www.tablesgenerator.com/markdown_tables


```bash
WORKING_DIR=/nfs/users/nfs_s/sd21/lustre118_link/ov_ltu/RADSEQ_2021

mkdir 04_REFMAP/INFO

cp ../RADseq_2017/DEMULTIPLEX/samples.popmap* 04_REFMAP/INFO/


cd ${WORKING_DIR}/00_SCRIPTS

bsub.py --queue yesterday 10 --threads 7 refmap "./run_ref-map.sh"

```


where "run_ref-map.sh" is:
```bash
#!/bin/bash

WORKING_DIR=/nfs/users/nfs_s/sd21/lustre118_link/ov_ltu/RADSEQ_2021

ref_map.pl \
--samples ${WORKING_DIR}/03_MAPPING \
 --popmap ${WORKING_DIR}/04_REFMAP/INFO/samples.popmap2 \
 -o ${WORKING_DIR}/04_REFMAP \
 -T 7 \
 --rm-pcr-duplicates

 ```
