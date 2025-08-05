# Apptainer Container Setup for RNA-seq Pipeline

This guide documents the setup of a writable Apptainer (formerly Singularity) container environment for an RNA-seq workflow on the UNC Longleaf HPC.

---

## ✅ Prerequisites

Load Apptainer on the cluster:

```bash
module add apptainer

# Create a sandbox Container
apptainer build --sandbox srrAlign/ docker://ubuntu:22.04
#Create the required mount directories for longleaf
mkdir -p srrAlign/nas
mkdir -p srrAlign/proj
mkdir -p srrAlign/work
mkdir -p srrAlign/users
mkdir -p srrAlign/overflow
mkdir -p srrAlign/datacommons

#start an interactive container using fakeroot
apptainer shell --writable --fakeroot --no-mount all srrAlign/

#update the system and get your basic tools
apt-get update
apt-get install -y build-essential curl wget git unzip ca-certificates
apt-get install zlib1g-dev
apt-get install xxd
apt-get install cmake
apt-get install libncurses-dev
apt-get install libbz2-dev
apt-get install liblzma-dev
#Tool Installation
# STAR
cd /opt
git clone --branch 2.7.11b https://github.com/alexdobin/STAR.git
cd STAR
make STAR
cp STAR /usr/local/bin/

# Salmon
wget https://github.com/COMBINE-lab/salmon/releases/download/v1.10.0/salmon-1.10.0_linux_x86_64.tar.gz
tar xzvf salmon-1.10.0_linux_x86_64.tar.gz
cp salmon-latest_linux_x86_64/bin/salmon /usr/local/bin

# samtools
wget https://github.com/samtools/samtools/releases/download/1.22.1/samtools-1.22.1.tar.bz2
tar -xjf samtools-1.20.tar.bz2
cd samtools-1.22.1
./configure
make -j16
make install
make -j16
make install

# trimmomatic
cd /opt
wget http://www.usadellab.org/cms/uploads/supplementary/Trimmomatic/Trimmomatic-0.39.zip
unzip Trimmomatic-0.39.zip
cp Trimmomatic-0.39/trimmomatic-0.39.jar /usr/local/bin/trimmomatic.jar
echo -e '#!/bin/bash\njava -jar /usr/local/bin/trimmomatic.jar "$@"' > /usr/local/bin/trimmomatic
chmod +x /usr/local/bin/trimmomatic

# r 4.4.0; this version of ubuntu ships with an earlier R version so we need to install it manually
apt-get install -y software-properties-common
add-apt-repository ppa:marutter/rrutter4.0
apt-get update
apt-get install -y r-base

#Next, install what we use in R:

```R
if (!require("BiocManager", quietly = TRUE))
    install.packages("BiocManager")

# The following initializes usage of Bioc devel
BiocManager::install(version='devel')

BiocManager::install("edgeR")
BiocManager::install("tximport")
install.packages("tidyverse")
quit()
#exit interactive mode
exit
```
#FINALLY build your .sif file
apptainer build srrAlign.sif srrAlign/
