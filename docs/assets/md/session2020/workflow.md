## FAIRbioinfo for bioinformaticians 2020

Authors: C. Hernandez T. Denecker J.Sellier C. Toffano-Nioche

### Introduction
For this practical exercise on Snakemake we will:

- access to snakemake by the way of a docker container 
- access to analysis tools by the way of a conda environment (details about conda will be seen after) 
- create a first snakefile with one rule
- add a second rule to create the first workflow

During these exercises, we will execute several cycles:  executing snakemake, observing the result and improving the code. Each cycle correspond to an objective. Each code version will be noted exoX.smk with X a progressive digit. 

### Exercice setup

We will access to Snakemake by running a docker image containing the conda tool (among other):

**docker miniconda3 (git 2.20.1 + conda 4.8.2)**
```
docker run -i -t -v ${PWD}:/data  continuumio/miniconda3
```
And, we will access to the analysis tools thanks to a conda environment, envfair.yml designed for this exercise

**envfair.yml**

```
channels:
    - conda-forge
    - bioconda
    - default
dependencies:
    - python =3.7.6 # specify  python  version 
    - snakemake-minimal=5.10.0 # workflow  manager
    - graphviz=2.42.3 # for  visualisation
    - xorg -libxrender
    - xorg -libxpm
    - wget=1.20.1 # for  downloading  files
    - fastqc=0.11.9 # for  the  RNAseq  analysis
    - bowtie2=2.4.1
    - samtools=1.101
    - subread=2.0.1
```
_NB: The specification of the python version is not required but can  help  with  downstream  conflicts._

**Conda environment**
```
conda  env  create  -n envfair  -f envfair.yml
conda  activate  envfair
```
### Objective 1: the rule concept, 1 input

Create a snakemake file named _exo1.smk_ including the first step of the RNAseq workflow (the reads quality checking thank to the fastqc tool) on one of the RNAseq files

---
***HINT***
- input file: SRR3099585_chr18.fastq.gz in a local directory of yours
- fastqc access:  by running docker miniconda3+ activate the conda envfair environment
- fastqc command:fastqc inputFileName --outdir ResultDirectory
- the 2 fastqc result files (.zip & .html) are named based on the prefix of input file
---

***Solution***
```
rule fastqc:
  output:
    "FastQC/SRR3099585_chr18_fastqc.zip", 
    "FastQC/SRR3099585_chr18_fastqc.html"
  input:
    "Data/SRR3099585_chr18.fastq.gz"
  shell: "fastqc --outdir FastQC/ {input}"
  ```
**Snakemake run**

```
snakemake  --snakefile  exo1.smk
```
***Oserve the results***

Look at the newly created FastQC directory:  Snakemake create needed directories.

### Objective 2: the rule concept, 2 inputs

Create snakemake file _exo2.smk_. Improve previous code: add a second input RNAseq file to the rule.

---
***HINT***
- input file: SRR3099586_chr18.fastq.gz in a local directory of yours
---

***Solution***

```
rule fastqc:
  output:
    "FastQC/SRR3099585_chr18_fastqc.zip", 
    "FastQC/SRR3099585_chr18_fastqc.html",
    "FastQC/SRR3099586_chr18_fastqc.zip", 
    "FastQC/SRR3099586_chr18_fastqc.html"
  input:
    "Data/SRR3099585_chr18.fastq.gz",
    "Data/SRR3099586_chr18.fastq.gz"
  shell: "fastqc --outdir FastQC/ {input}"
  ```
**Snakemake run**

```
# -s is the short form of the --snakefile option
snakemake -s exo2.smk
```
***Oserve the results***

Why does Snakemake reply "Nothing to be done"?
Two solutions:
- delete the FastQC directory (rm -Rf FastQC) and rerun the snakemake command 
- use the Snakemake --forcerules(-R) option: 
 
```
snakemake -s exo2.smk -R fastqc
```

### Objective 3: expand function, n inputs

Create snakemake file _exo3.smk_. Improve previous code: add all the RNAseq files. Boring with writing all input and output file names? Use the expand()function to manage all the input RNAseq files at once.

---
***HINT***
- create a Python list at the begining of the snakefile and containing all the basename of the input files (don’t include the ”.fastq.gz” suffix). Python list: list_name = ["item1", "item2", ..., "itemN"]
- replace the filename lists of the input and output directives by the expand()function
---


***Solution***

```
SAMPLES = ["SRR3099585_chr18","SRR3099586_chr18","SRR3099587_chr18"]

rule fastqc:
  output:
    expand("FastQC/{sample}_fastqc.zip", sample = SAMPLES),
    expand("FastQC/{sample}_fastqc.html", sample = SAMPLES)
  input:
    expand("Data/{sample}.fastq.gz", sample = SAMPLES)
  shell: "fastqc --outdir FastQC/ {input}"
```
**Snakemake run**

```
rm -Rf  FastQC/
snakemake  -s exo3.smk
```

### Objective 4: 2 rules, no link

Create snakemake file _exo4.smk_. Improve previous code: add a second rule, this will start a workflow. The second rule concerns the creation of an index file for the genome sequence (needed for the mapping step).  As the mapping tool is bowtie2,the index creation tool is bowtie2-build.

---
***HINT***
- genome file (input): Data/O.tauri_genome.fna
- use a Python list and the expand()function to manage the 6 index file names that will be created by the command:  "*.1.bt2" ... "*.4.bt2","*.rev.1.bt2","*.rev.2.bt2"
- command: `bowtie2-build genomeSequenceAccess indexAccessPrefix`
---

***Solution***

```
SAMPLES = ["SRR3099585_chr18","SRR3099586_chr18","SRR3099587_chr18"]
BIDX = ["1","2","3","4","rev.1","rev.2"]

rule genome_bwt2_index:
  output:
    expand("Tmp/Otauri.{ext}.bt2", ext=BIDX)
  input:
    "Data/O.tauri_genome.fna"
  shell: "bowtie2-build {input} Tmp/Otauri"

rule fastqc:
  output:
    expand("FastQC/{sample}_fastqc.zip", sample = SAMPLES),
    expand("FastQC/{sample}_fastqc.html", sample = SAMPLES)
  input:
    expand("Data/{sample}.fastq.gz", sample = SAMPLES)
  shell: "fastqc --outdir FastQC/ {input}"
```

**Snakemake run**

```
rm -Rf  FastQC/
snakemake  -s exo4.smk
```

***Observe the results***

Does Snakemake do the job? Why wasn’t the fastqc command launched?

***rule links***

Snakemake run the first rule and stop when the target files are present.Also, there is no link between the 2 rules because they concern two independent parts of the analysis.The solution is to add a rule that aggregate this 2 parts of the workflow


### Objective 5: The target rule

Create snakemake file _exo5.smk_. Improve previous code: add a "first" rule (rule all, target, ...)  with the expected results for the 2rules (fastqc and genome_bwt2_index in its input: directive.

***Solution***

```
SAMPLES = ["SRR3099585_chr18","SRR3099586_chr18","SRR3099587_chr18"]
BIDX = ["1","2","3","4","rev.1","rev.2"]

rule all:
  input:
    expand("FastQC/{sample}_fastqc.html", sample=SAMPLES),
    expand("Tmp/Otauri.{ext}.bt2", ext=BIDX)

rule genome_bwt2_index:
  output:
    expand("Tmp/Otauri.{ext}.bt2", ext=BIDX)
  input:
    "Data/O.tauri_genome.fna"
  shell: "bowtie2-build {input} Tmp/Otauri"

rule fastqc:
  output:
    expand("FastQC/{sample}_fastqc.zip", sample = SAMPLES),
    expand("FastQC/{sample}_fastqc.html", sample = SAMPLES)
  input:
    expand("Data/{sample}.fastq.gz", sample = SAMPLES)
  shell: "fastqc --outdir FastQC/ {input}"
```
**Snakemake run**

```
rm -Rf  FastQC/
snakemake  -s exo5.smk -R all  fastqc
```

***Observe the results***
Does Snakemake do the job?

***Fastqc:  job or jobs?***
Look at more precisely the fastqc job.  We have many input files but snakemake launched only one fastqc job:

![image](https://github.com/chernan/FAIR_bioinfo_docs/blob/master/03_workflow/images/FAIR_ex1_o5_smk.png)

It is because thefastqcrule is defined with a list of files and not for one unique file and because thefastqc tool accepts both a unique file as wellas a list of files.


### Objective 6: Running n individual jobs

Create snakemake file _exo6.smk_. Improve previous code: Thank to the all rule, all expected files are designated.  So we don’t need to give the fastqc rule a list anymore and we can replace it to manage only one file and all files one by one.  We will gain in power in system shaving more than one core.

---
***HINT***
Replace the expand()function with a wildcard for one filename in the fastqc rule.

---

**Solution**

```
SAMPLES = ["SRR3099585_chr18","SRR3099586_chr18","SRR3099587_chr18"]
BIDX = ["1","2","3","4","rev.1","rev.2"]

rule all:
  input:
    expand("FastQC/{sample}_fastqc.html", sample=SAMPLES),
    expand("Tmp/Otauri.{ext}.bt2", ext=BIDX)

rule genome_bwt2_index:
  output:
    expand("Tmp/Otauri.{ext}.bt2", ext=BIDX)
  input:
    "Data/O.tauri_genome.fna"
  shell: "bowtie2-build {input} Tmp/Otauri"

rule fastqc:
  output:
    "FastQC/{sample}_fastqc.zip",
    "FastQC/{sample}_fastqc.html"
  input:
    "Data/{sample}.fastq.gz"
  shell: "fastqc --outdir FastQC/ {input}"
```

**Snakemake run**

```
rm -Rf  FastQC/
snakemake  -s exo6.smk -R all fastqc
```

***Observe the results***
Now Snakemake did many fastqc jobs:

![image](https://github.com/chernan/FAIR_bioinfo_docs/blob/master/03_workflow/images/FAIR_ex1_o6_smk.png)

But what happens to the runtime displays on the screen? To correct this, we will move the displays to a log file specific for each rule and each input file.

### Objective 7: Adding log file

Create snakemake file _exo7.smk_. Improve previous code: 
In Unix systems, the output of a command is usually sent to two separate streams:  the normal output:  to Standard Out (stdout also ">" in shell),and error messages:  to Standard Error (stderr, or "2>" in shell).  To integrate stderr into the same log file as the stdout can be use "&>" instead of ">":
shell:  ... &> {log}, but use with care when output files are printed tostdout (as often in shell comands). Redirect the stdout and stderr streams of the fastqc and bowtie2-build commands.

---
***HINT***
For the bowtie2-build and fastqc rules, add the log: directive with two variables (log1 and log2) to redirect each streams.

---

**Solution**

```
SAMPLES = ["SRR3099585_chr18","SRR3099586_chr18","SRR3099587_chr18"]
BIDX = ["1","2","3","4","rev.1","rev.2"]

rule all:
  input:
    expand("FastQC/{sample}_fastqc.html", sample=SAMPLES),
    expand("Tmp/Otauri.{ext}.bt2", ext=BIDX)

rule genome_bwt2_index:
  output:
    expand("Tmp/Otauri.{ext}.bt2", ext=BIDX)
  input:
    "Data/O.tauri_genome.fna"
  log:
    log1="Logs/genome_bwt2_index.log1",
    log2="Logs/genome_bwt2_index.log2"
  shell: "bowtie2-build {input} Tmp/Otauri 1>{log.log1} 2>{log.log2}"

rule fastqc:
  output:
    "FastQC/{sample}_fastqc.zip",
    "FastQC/{sample}_fastqc.html"
  input:
    "Data/{sample}.fastq.gz"
  log:
    log1="Logs/{sample}_fastqc.log1",
    log2="Logs/{sample}_fastqc.log2"
  shell: "fastqc --outdir FastQC/ {input} 1>{log.log1} 2>{log.log2}"
```
**Snakemake run**

```
rm -Rf  FastQC/ Tmp/ Logs/
snakemake  -s exo7.smk
```
### Setup second part
#### Data

-g genome sequence access (including extention .fna, .fasta)
-a genome annotation access (inluding extention .gff)
-d RNAseq sample prefixnext args:  RNAseq sample prefix, no .fastq.gz extention

#### Bash command line

```
FAIR_initial_script.sh -g ../O.tauri_genome.fna -a ../O.tauri_annotation.gff -d ../SRR3099585_chr18 S*86_chr18 S*87_chr18 S*97 _chr18 S*98_chr18 S*99_chr18

```
#### Script in 3 main blocks

1) while  getops  do ...  done
2) for  sample  in $* ; do ...  done
3)  creation  of the  result  file , counts.txt , with  paste , awk ,and sed  bash  commands

#### getops block

```
while  getopts g:a:d: flag do
        case  $flag  in
            g)   genome=$OPTARG4echo  genome  is  $genome  ;;
            a)   annots=$OPTARG6echo  annotation  is  $annots  ;;
            d)   rnadir=$OPTARG8echo  RNAseq  path is  $rnadir  ;;
            :)   echo "L’option  $OPTARG  requiert  un  argument"10exit 1 ;;
            \?)  echo "$OPTARG : option  invalide"12exit 1 ;;
            esac
        done
    shift $((  OPTIND  - 1 ))   # shift  past  the  last  flag or argument

echo  samples  are $*

nbs=0;
for  sample  in $* ; do
    nbs=$(expr ${nbs} + 1)
    echo  traitement  of  sample ${sample}
    # --------- quality  control  of  reads
    if [ ! -d FastQC  ]; then
        mkdir  FastQC
    fi
    fastqc  --outdir  FastQC ${rnadir}${sample }.fastq.gz >FastQC/${sample }.log 2>&1
    #---------- reads  mapping
    if [ ! -d Bwt2_index  ]; then
        mkdir  Bwt2_index
        bowtie2 -build ${genome} Bwt2_index/tauri > Bwt2_index/Bwt2_index.log 2>&1
    fi
    bowtie2  -x Bwt2_index/tauri -U ${rnadir}${sample }. fastq.gz -S ${sample }.sam > ${sample}_bowtie2.log 2>&1
    #---------- selection  and  format  modification
    samtools  view -b ${sample }.sam -o ${sample }.bam
    samtools  sort ${sample }.bam -o ${sample}_sort.bam
    samtools  index ${sample}_sort.bam
    #---------- counting  of  mapped  reads by gene
    featureCounts  -t gene -g ID -a ${annots} -s 2 -o ${sample}_ftc.txt ${sample}_sort.bam > ${sample}_ftc.log 2>&1
done
```

```
# Count table block
paste *_ftc.txt > ftc_tmp.txt
awk -v nb=${nbs} -v col=7 ’BEGIN{FS="\t"}{ ctmp=$1; for(i=col;i<=nb*col;i=i+col){count=sprintf ("%s\t%s",ctmp ,$i);ctmp=count}; print  count}’ ftc_tmp.txt | sed 1d > counts.txt
```



### Objective 8: Adding a configuration file

Create snakemake file _exo8.smk_. Improve previous code: add a configuration file, named RNAseq.yml, containing both the genome sequence and the annotation files manes, and the access to the Data directory. In the snakefile, change the configured variables (ex.  replace Data/ and genome by their config[] values).  The Python strings concatenation is + 
Then, run snakemake with the `--configfile option`

**Configuration file: exo8.yml**

```
genome:
  O.tauri_genome.fna
annots:
  O.tauri_annotation.gff
dataDir:
  Data/
```
**Solution**

```
SAMPLES = ["SRR3099585_chr18","SRR3099586_chr18","SRR3099587_chr18"]
BIDX = ["1","2","3","4","rev.1","rev.2"]

rule all:
  input:
    expand("FastQC/{sample}_fastqc.html", sample=SAMPLES),
    expand("Tmp/Otauri.{ext}.bt2", ext=BIDX)

rule genome_bwt2_index:
  output:
    expand("Tmp/Otauri.{ext}.bt2", ext=BIDX)
  input:
    config["dataDir"]+config["genome"]
  log:
    log1="Logs/genome_bwt2_index.log1",
    log2="Logs/genome_bwt2_index.log2"
  shell: "bowtie2-build {input} Tmp/Otauri 1>{log.log1} 2>{log.log2}"

rule fastqc:
  output: 
    "FastQC/{sample}_fastqc.zip",
    "FastQC/{sample}_fastqc.html"
  input: 
    config["dataDir"]+"{sample}.fastq.gz"
  log:
    log1="Logs/{sample}_fastqc.log1",
    log2="Logs/{sample}_fastqc.log2"
  shell: "fastqc --outdir FastQC/ {input} 1>{log.log1} 2>{log.log2}"
```
**Snakemake run**

```
rm -Rf  FastQC/ Result/ Tmp/ Logs/ ; snakemake  -s exo8.smk--configfile  exo8.yml
```
### Objective 9: for block
Shell script
```
nbs =0;
for sample in $* ; do
...
done
```
To manage all \*.fastq.gz files in a directory, use the glob_wildcards() function. In ex2_o2.smk, replace the SAMPLES definition by:
```
SAMPLES , = glob_wildcards ( config [" dataDir "]+"{ sample }. fastq .
gz ")
```

**Solution**
```
SAMPLES, = glob_wildcards(config["dataDir"]+"{sample}.fastq.gz")
BIDX = ["1","2","3","4","rev.1","rev.2"]

rule all:
  input:
    expand("FastQC/{sample}_fastqc.html", sample=SAMPLES),
    expand("Tmp/Otauri.{ext}.bt2", ext=BIDX)

rule genome_bwt2_index:
  output:
    expand("Tmp/Otauri.{ext}.bt2", ext=BIDX)
  input:
    config["dataDir"]+config["genome"]
  log:
    log1="Logs/genome_bwt2_index.log1",
    log2="Logs/genome_bwt2_index.log2"
  shell: "bowtie2-build {input} Tmp/Otauri 1>{log.log1} 2>{log.log2}"

rule fastqc:
  output: 
    "FastQC/{sample}_fastqc.zip",
    "FastQC/{sample}_fastqc.html"
  input: 
    config["dataDir"]+"{sample}.fastq.gz"
  log:
    log1="Logs/{sample}_fastqc.log1",
    log2="Logs/{sample}_fastqc.log2"
  shell: "fastqc --outdir FastQC/ {input} 1>{log.log1} 2>{log.log2}"
```
### Objective 10: Quality control, fastqc
Shell script
```
if [ ! -d FastQC ]; then
mkdir FastQC
fi
fastqc -- outdir FastQC ${ sample }. fastq .gz > FastQC /${ sample
}. log 2 >&1
```
No more need to test the existence of a directory, it is created as needed.

**rule fastqc:**
This rule is already present in the snakefile

### Objective 11: Reads mapping, bowtie2
Shell script
```
if [ ! -d Bwt2_index ]; then
mkdir Bwt2_index
bowtie2 - build ${ genome } Bwt2_index / tauri > Bwt2_index /
Bwt2_index .log 2 >&1
fi
bowtie2 -x Bwt2_index / tauri -U ${ sample }. fastq .gz -S ${
sample }. sam > ${ sample } _bowtie2 . log 2 >&1
```

**Solution**
add mapping rule + use {input[0]} + log err only
```
SAMPLES, = glob_wildcards(config["dataDir"]+"{sample}.fastq.gz")
BIDX = ["1","2","3","4","rev.1","rev.2"]


rule bwt2_mapping:
  output:
    "Tmp/{sample}.sam"
  input:
    config["dataDir"]+"{sample}.fastq.gz",
    expand("Tmp/Otauri.{ext}.bt2", ext=BIDX)
  log:
    "Logs/{sample}_bwt2_mapping.log"
  shell: "bowtie2 -x Tmp/Otauri -U {input[0]} -S {output} 2> {log} "

rule genome_bwt2_index:
  output:
    expand("Tmp/Otauri.{ext}.bt2", ext=BIDX)
  input:
    config["dataDir"]+config["genome"]
  log:
    log1="Logs/genome_bwt2_index.log1",
    log2="Logs/genome_bwt2_index.log2"
  shell: "bowtie2-build {input} Tmp/Otauri 1>{log.log1} 2>{log.log2}"

rule fastqc:
  output: 
    "FastQC/{sample}_fastqc.zip",
    "FastQC/{sample}_fastqc.html"
  input: 
    config["dataDir"]+"{sample}.fastq.gz"
  log:
    log1="Logs/{sample}_fastqc.log1",
    log2="Logs/{sample}_fastqc.log2"
  shell: "fastqc --outdir FastQC/ {input} 1>{log.log1} 2>{log.log2}"
```

Why some troubles?
The snakemake launch probably didn't do what was expected. What have we forgotten?
We added a new rule to a snakemake but we didn't manage the rule tree, their is no input-output link to include the new rule to the workflow.
We will do that by completing the input directive of the target rule (caution to respect the Python "list" structure, coma-separated).


**Solution**
```
SAMPLES, = glob_wildcards(config["dataDir"]+"{sample}.fastq.gz")
BIDX = ["1","2","3","4","rev.1","rev.2"]

rule all:
  input:
    expand("FastQC/{sample}_fastqc.html", sample=SAMPLES),
    expand("Tmp/Otauri.{ext}.bt2", ext=BIDX),
    expand("Tmp/{sample}.sam", sample=SAMPLES)

rule bwt2_mapping:
  output:
    "Tmp/{sample}.sam"
  input:
    config["dataDir"]+"{sample}.fastq.gz",
    expand("Tmp/Otauri.{ext}.bt2", ext=BIDX)
  log:
    "Logs/{sample}_bwt2_mapping.log"
  shell: "bowtie2 -x Tmp/Otauri -U {input[0]} -S {output} 2> {log} "

rule genome_bwt2_index:
  output:
    expand("Tmp/Otauri.{ext}.bt2", ext=BIDX)
  input:
    config["dataDir"]+config["genome"]
  log:
    log1="Logs/genome_bwt2_index.log1",
    log2="Logs/genome_bwt2_index.log2"
  shell: "bowtie2-build {input} Tmp/Otauri 1>{log.log1} 2>{log.log2}"

rule fastqc:
  output: 
    "FastQC/{sample}_fastqc.zip",
    "FastQC/{sample}_fastqc.html"
  input: 
    config["dataDir"]+"{sample}.fastq.gz"
  log:
    log1="Logs/{sample}_fastqc.log1",
    log2="Logs/{sample}_fastqc.log2"
  shell: "fastqc --outdir FastQC/ {input} 1>{log.log1} 2>{log.log2}"
```



### Objective 11: samtools
Shell script
```
samtools sort -O bam -o ${ sample } _sort .bam ${ sample }. sam
samtools index ${ sample } _sort .bam
```


**Solution**
```
SAMPLES, = glob_wildcards(config["dataDir"]+"{sample}.fastq.gz")
BIDX = ["1","2","3","4","rev.1","rev.2"]

rule all:
  input:
    expand("FastQC/{sample}_fastqc.html", sample=SAMPLES),
    expand("Tmp/Otauri.{ext}.bt2", ext=BIDX),
    expand("Tmp/{sample}.sam", sample=SAMPLES),
    expand("Result/{sample}_sort.bam.bai", sample=SAMPLES)

rule sam2bam_sort:
  output:
    bam="Result/{sample}_sort.bam",
    bai="Result/{sample}_sort.bam.bai"
  input:
    "Tmp/{sample}.sam"
  log:
    sort="Logs/{sample}_sam2bam_sort.log",
    index="Logs/{sample}_bam2bai.log"
  shell: 
    "samtools sort -O bam -o {output.bam} {input} 2> {log.sort} ;"
    "samtools index {output.bam} 2> {log.index}"

rule bwt2_mapping:
  output:
    "Tmp/{sample}.sam"
  input:
    config["dataDir"]+"{sample}.fastq.gz",
    expand("Tmp/Otauri.{ext}.bt2", ext=BIDX)
  log:
    "Logs/{sample}_bwt2_mapping.log"
  shell: "bowtie2 -x Tmp/Otauri -U {input[0]} -S {output} 2> {log} "

rule genome_bwt2_index:
  output:
    expand("Tmp/Otauri.{ext}.bt2", ext=BIDX)
  input:
    config["dataDir"]+config["genome"]
  log:
    log1="Logs/genome_bwt2_index.log1",
    log2="Logs/genome_bwt2_index.log2"
  shell: "bowtie2-build {input} Tmp/Otauri 1>{log.log1} 2>{log.log2}"

rule fastqc:
  output: 
    "FastQC/{sample}_fastqc.zip",
    "FastQC/{sample}_fastqc.html"
  input: 
    config["dataDir"]+"{sample}.fastq.gz"
  log:
    log1="Logs/{sample}_fastqc.log1",
    log2="Logs/{sample}_fastqc.log2"
  shell: "fastqc --outdir FastQC/ {input} 1>{log.log1} 2>{log.log2}"
```

### Objective 12: FeatureCount
Shell script
```
featureCounts -t gene -g ID -a ${ annots } -s  -o ${ sample }
_ftc . txt ${ sample } _sort . bam > ${ sample } _ftc . log 2 >&1
```

**Solution**
add featureCount rule, params directive
```
SAMPLES, = glob_wildcards(config["dataDir"]+"{sample}.fastq.gz")
BIDX = ["1","2","3","4","rev.1","rev.2"]

rule all:
  input:
    expand("FastQC/{sample}_fastqc.html", sample=SAMPLES),
    expand("Tmp/Otauri.{ext}.bt2", ext=BIDX),
    expand("Tmp/{sample}.sam", sample=SAMPLES),
    expand("Result/{sample}_sort.bam.bai", sample=SAMPLES),
    expand("Tmp/{sample}_ftc.txt", sample=SAMPLES)

rule counting:
  output:
    "Tmp/{sample}_ftc.txt"
  input:
    bam="Result/{sample}_sort.bam",
    annot=config["dataDir"]+config["annots"]
  params: t="gene", g="ID", s="2"
    "Logs/{sample}_counts.log"
  shell:
    "featureCounts -t {params.t} -g {params.g} -a {input.annot} -s {params.s} -o {output} {input.bam} &> {log}"

rule sam2bam_sort:
  output:
    bam="Result/{sample}_sort.bam",
    bai="Result/{sample}_sort.bam.bai"
  input:
    "Tmp/{sample}.sam"
  log:
    sort="Logs/{sample}_sam2bam_sort.log",
    index="Logs/{sample}_bam2bai.log"
  shell: 
    "samtools sort -O bam -o {output.bam} {input} 2> {log.sort} ;"
    "samtools index {output.bam} 2> {log.index}"


rule bwt2_mapping:
  output:
    "Tmp/{sample}.sam"
  input:
    config["dataDir"]+"{sample}.fastq.gz",
    expand("Tmp/Otauri.{ext}.bt2", ext=BIDX)
  log:
    "Logs/{sample}_bwt2_mapping.log"
  shell: "bowtie2 -x Tmp/Otauri -U {input[0]} -S {output} 2> {log} "

rule genome_bwt2_index:
  output:
    expand("Tmp/Otauri.{ext}.bt2", ext=BIDX)
  input:
    config["dataDir"]+config["genome"]
  log:
    log1="Logs/genome_bwt2_index.log1",
    log2="Logs/genome_bwt2_index.log2"
  shell: "bowtie2-build {input} Tmp/Otauri 1>{log.log1} 2>{log.log2}"

rule fastqc:
  output: 
    "FastQC/{sample}_fastqc.zip",
    "FastQC/{sample}_fastqc.html"
  input: 
    config["dataDir"]+"{sample}.fastq.gz"
  log:
    log1="Logs/{sample}_fastqc.log1",
    log2="Logs/{sample}_fastqc.log2"
  shell: "fastqc --outdir FastQC/ {input} 1>{log.log1} 2>{log.log2}"
```
### Objective 13: Count matrix creation

Shell script
```
paste * _ftc .txt > counts_tmp .txt
awk -v nb=${ nb_sample } 'BEGIN {FS ="\ t"}{ count_tmp =$1; for (i
=7;i <= nb *7; i=i+7) { count = sprintf ("% s\t%s", count_tmp ,$i);
count_tmp = count }; print count }' counts_tmp .txt | sed 1d >
counts . txt
```
---
***HINT***
Create 2 rules to manage some files aggregation to one result file:
- rule extract_counts: extract geneID and counts in individual files
- rule matrix_counts: paste these files
---

**Solution**
add matrix count rule (2 rules to manage many files aggregation to one)
```
SAMPLES, = glob_wildcards(config["dataDir"]+"{sample}.fastq.gz")
BIDX = ["1","2","3","4","rev.1","rev.2"]

rule all:
  input:
    expand("FastQC/{sample}_fastqc.html", sample=SAMPLES),
    expand("Tmp/Otauri.{ext}.bt2", ext=BIDX),
    expand("Tmp/{sample}.sam", sample=SAMPLES),
    expand("Result/{sample}_sort.bam.bai", sample=SAMPLES),
    expand("Tmp/{sample}_ftc.txt", sample=SAMPLES),
    "Result/counts_matrix.txt"

rule matrix_counts:
  output:
    "Result/counts_matrix.txt"
  input:
    countfile=expand("Tmp/{sample}_ftc7.txt", sample=SAMPLES),
    geneID=expand("Tmp/{sample}_ftc1.txt", sample=SAMPLES)
  log:
    "Logs/matrix_counts.log"
  shell:
    """cp {input.geneID[0]} Tmp/ftc_geneID.txt ; paste Tmp/ftc_geneID.txt {input.countfile} > {output} &> {log}"""

rule extract_counts:
  output:
    col7="Tmp/{sample}_ftc7.txt",
    col1="Tmp/{sample}_ftc1.txt"
  input:
    "Tmp/{sample}_ftc.txt"
  log:
    "Logs/{sample}_extract_counts.log"
  shell: """cut -f 7- {input} | sed 1d > {output.col7} &> {log} ; cut -f 1 {input} | sed 1d > {output.col1} """

rule counting:
  output:
    "Tmp/{sample}_ftc.txt"
  input:
    bam="Result/{sample}_sort.bam",
    annot=config["dataDir"]+config["annots"]
  params: t="gene", g="ID", s="2"
  log:
    "Logs/{sample}_counts.log"
  shell:
    "featureCounts -t {params.t} -g {params.g} -a {input.annot} -s {params.s} -o {output} {input.bam} &> {log}"

rule sam2bam_sort:
  output:
    bam="Result/{sample}_sort.bam",
    bai="Result/{sample}_sort.bam.bai"
  input:
    "Tmp/{sample}.sam"
  log:
    sort="Logs/{sample}_sam2bam_sort.log",
    index="Logs/{sample}_bam2bai.log"
  shell: 
    "samtools sort -O bam -o {output.bam} {input} 2> {log.sort} ;"
    "samtools index {output.bam} 2> {log.index}"


rule bwt2_mapping:
  output:
    "Tmp/{sample}.sam"
  input:
    config["dataDir"]+"{sample}.fastq.gz",
    expand("Tmp/Otauri.{ext}.bt2", ext=BIDX)
  log:
    "Logs/{sample}_bwt2_mapping.log"
  shell: "bowtie2 -x Tmp/Otauri -U {input[0]} -S {output} 2> {log} "

rule genome_bwt2_index:
  output:
    expand("Tmp/Otauri.{ext}.bt2", ext=BIDX)
  input:
    config["dataDir"]+config["genome"]
  log:
    log1="Logs/genome_bwt2_index.log1",
    log2="Logs/genome_bwt2_index.log2"
  shell: "bowtie2-build {input} Tmp/Otauri 1>{log.log1} 2>{log.log2}"

rule fastqc:
  output: 
    "FastQC/{sample}_fastqc.zip",
    "FastQC/{sample}_fastqc.html"
  input: 
    config["dataDir"]+"{sample}.fastq.gz"
  log:
    log1="Logs/{sample}_fastqc.log1",
    log2="Logs/{sample}_fastqc.log2"
  shell: "fastqc --outdir FastQC/ {input} 1>{log.log1} 2>{log.log2}"
```


ex2_o7.yml
```
channels:
  - conda-forge
  - bioconda
  - main
  - r
  - default
dependencies:
  # Specify python version (not required but can help with downstream conflicts)
  - python=3.7.6
  # The workflow manager
  - snakemake-minimal=5.10.0
  # For visualizing workflows
  - graphviz=2.42.3
  - xorg-libxrender
  - xorg-libxpm
  # For downloading files
  - wget=1.20.1
  # For the RNAseq analysis
  - fastqc=0.11.9
  - bowtie2=2.4.1
  - samtools=1.10
  - subread=2.0.1
```

ex2_o7.smk
add conda: "xx.yml"
```
SAMPLES, = glob_wildcards(config["dataDir"]+"{sample}.fastq.gz")
BIDX = ["1","2","3","4","rev.1","rev.2"]

rule all:
  input:
    expand("FastQC/{sample}_fastqc.html", sample=SAMPLES),
    expand("Tmp/Otauri.{ext}.bt2", ext=BIDX),
    expand("Tmp/{sample}.sam", sample=SAMPLES),
    expand("Result/{sample}_sort.bam.bai", sample=SAMPLES),
    expand("Tmp/{sample}_ftc.txt", sample=SAMPLES),
    "Result/counts_matrix.txt"

rule matrix_counts:
  output:
    "Result/counts_matrix.txt"
  input:
    countfile=expand("Tmp/{sample}_ftc7.txt", sample=SAMPLES),
    geneID=expand("Tmp/{sample}_ftc1.txt", sample=SAMPLES)
  log:
    "Logs/matrix_counts.log"
  shell:
    """cp {input.geneID[0]} Tmp/ftc_geneID.txt ; paste Tmp/ftc_geneID.txt {input.countfile} > {output} &> {log}"""

rule extract_counts:
  output:
    col7="Tmp/{sample}_ftc7.txt",
    col1="Tmp/{sample}_ftc1.txt"
  input:
    "Tmp/{sample}_ftc.txt"
  log:
    "Logs/{sample}_extract_counts.log"
  shell: """cut -f 7- {input} | sed 1d > {output.col7} &> {log} ; cut -f 1 {input} | sed 1d > {output.col1} """

rule counting:
  output:
    "Tmp/{sample}_ftc.txt"
  input:
    bam="Result/{sample}_sort.bam",
    annot=config["dataDir"]+config["annots"]
  params: t="gene", g="ID", s="2"
  log:
    "Logs/{sample}_counts.log"
  conda: "condaEnv4SmkRules/counting.yml"
  shell:
    "featureCounts -t {params.t} -g {params.g} -a {input.annot} -s {params.s} -o {output} {input.bam} &> {log}"

rule sam2bam_sort:
  output:
    bam="Result/{sample}_sort.bam",
    bai="Result/{sample}_sort.bam.bai"
  input:
    "Tmp/{sample}.sam"
  log:
    sort="Logs/{sample}_sam2bam_sort.log",
    index="Logs/{sample}_bam2bai.log"
  conda: "condaEnv4SmkRules/sam2bam_sort.yml"
  shell: 
    "samtools sort -O bam -o {output.bam} {input} 2> {log.sort} ;"
    "samtools index {output.bam} 2> {log.index}"


rule bwt2_mapping:
  output:
    "Tmp/{sample}.sam"
  input:
    config["dataDir"]+"{sample}.fastq.gz",
    expand("Tmp/Otauri.{ext}.bt2", ext=BIDX)
  log:
    "Logs/{sample}_bwt2_mapping.log"
  conda: "condaEnv4SmkRules/bwt2_mapping.yml"
  shell: "bowtie2 -x Tmp/Otauri -U {input[0]} -S {output} 2> {log} "

rule genome_bwt2_index:
  output:
    expand("Tmp/Otauri.{ext}.bt2", ext=BIDX)
  input:
    config["dataDir"]+config["genome"]
  log:
    log1="Logs/genome_bwt2_index.log1",
    log2="Logs/genome_bwt2_index.log2"
  conda: "condaEnv4SmkRules/bwt2_mapping.yml"
  shell: "bowtie2-build {input} Tmp/Otauri 1>{log.log1} 2>{log.log2}"

rule fastqc:
  output: 
    "FastQC/{sample}_fastqc.zip",
    "FastQC/{sample}_fastqc.html"
  input: 
    config["dataDir"]+"{sample}.fastq.gz"
  log:
    log1="Logs/{sample}_fastqc.log1",
    log2="Logs/{sample}_fastqc.log2"
  conda: "condaEnv4SmkRules/fastqc.yml"
  shell: "fastqc --outdir FastQC/ {input} 1>{log.log1} 2>{log.log2}"
```

### DEseq2
Clean delete and re-run
```
cp ex2_o8 . smk RNAseq_analysis . smk
cp ex2_o1 . yml RNAseq_analysis_smkEnv . yml
rm -Rf FastQC / Results / Logs / Tmp/
snakemake -s RNAseq_analysis .smk -- configfile
RNAseq_analysis_smkEnv .yml
```

