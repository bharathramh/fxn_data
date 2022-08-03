# 1 - Alignment to REF sequence 
We have made a synthetic reference (fxn.fa and fxn.ontarget.fa) to align the the reads; 
Using samtools we removed supplementary and secondary reads using the flags from samtools and sorted using samtools sort. 
And indexing the bam file using samtools index. creating the sam file for STRique using `samtools view -S`

```
minimap2 -a -x map-ont -t 40 fxn.fa <sample.fastq> | samtools view -bSh -F 3844 - | samtools sort -o <sample_sorted.bam> -
samtools index <sample_sorted.bam>
samtools view -Sh $i <sample_sorted.bam> > <sample_sorted.fxn.sam>
```
Run the following steps before the nanopore base file location
And refer the [STRique documentation](https://strique.readthedocs.io/en/latest/examples/intro/) for better clarrification and tool related doubts.

# 2 - Setting up STRique using docker
using docker we can set up the STRique latest version in the system.

```
docker pull giesselmann/strique:v0.3.0
docker run -it --mount type=bind,source=$(pwd),target=/host giesselmann/strique:v0.3.0
```

## 2.1 docker - help
To enter inside the docker using docker container use, 
`docker exec -it <container_id> /bin/bash` 

to list the container of docker , 
`docker ps`


# 3 - STRique indexing fast5 file
we can use the following command to list and index the FAST5 files from the sequencing run.

```
python3 /app/scripts/STRique.py index --recursive /host/<nanopore base file location>/ > /host/reads.fofn
```

# 4 - STRique count the repeats
creating output directory inside base location

```
mkdir /host/<nanopore base file location>/output 
cat <sample_sorted.fxn.sam> | python3 /app/scripts/STRique.py count /host/reads.fofn /app/models/r9_4_450bps.model repeat_config.tsv > /host/output/${n%%.*}.hg19.strique.tsv
```

# 5 - STRique - finding the length of two alleles
we can find A1 and A2 length by identifying "the mode" of both peaks using either R or python 
