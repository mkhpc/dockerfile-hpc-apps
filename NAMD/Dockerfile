###############################################################################
# Ubuntu 18.04, Intel oneAPI 2021.6, and NAMD 2.14
#
# Build with,
# docker build -t namd:2.14-intel-ubuntu18.04 -f ./Dockerfile
#
# NAMD Reference,
# J. C. Phillips, D. J. Hardy, J. D. C. Maia, J. E. Stone, J. V. Ribeiro, R. C. Bernardi, R. Buch, G. Fiorin, J. Henin, W. Jiang, R. McGreevy, M. C. R. Melo, B. K. Radak, R. D. Skeel, A. Singharoy, Y. Wang, B. Roux, A. Aksimentiev, Z. Luthey-Schulten, L. V. Kale, K. Schulten, C. Chipot and E. Tajkhorshid, “Scalable molecular dynamics on CPU and GPU architectures with NAMD,” Journal of Chemical Physics, 153:044130, 2020.
###############################################################################
# Build stage
###############################################################################
FROM mandeepkumar/compiler:intel-oneapi-2021.1-ubuntu18.04 as builder

# Update according to https://www.ks.uiuc.edu/Research/namd
ARG NAMD_VERSION=2.14

# Number of make jobs during compile
ARG JOBS=8

# Download sources
RUN mkdir -p /namd 
WORKDIR /namd
RUN curl -o namd.tar.gz https://www.ks.uiuc.edu/Research/namd/2.14/download/946183/NAMD_2.14_Source.tar.gz &&\ 
    tar -xzvf namd.tar.gz &&\
    mv NAMD_2.14_Source/* . &&\
    rm -fr NAMD_2.14_Source &&\
    curl -OL https://github.com/UIUC-PPL/charm/archive/refs/tags/v6.8.2.tar.gz &&\
    tar -xzvf v6.8.2.tar.gz &&\
    curl -o tcl-threaded.tar.gz http://www.ks.uiuc.edu/Research/namd/libraries/tcl8.5.9-linux-x86_64-threaded.tar.gz &&\
    tar -xzvf tcl-threaded.tar.gz &&\
    mv tcl8.5.9-linux-x86_64-threaded tcl-threaded &&\
    rm -fr Make.charm &&\
    rm -fr arch/Linux-x86_64-icc.arch &&\
    rm -fr arch/Linux-x86_64.tcl 

COPY Make.charm /namd
COPY Linux-x86_64-icc.arch /namd/arch 
COPY Linux-x86_64.tcl /namd/arch 

RUN cd /namd \
  && cd charm-6.8.2 \
  && env CC=icc CXX=icpc MPICC=mpiicc MPICXX=mpiicpc ./build charm++ mpi-linux-x86_64 icc ifort --with-production \
  && cd mpi-linux-x86_64-ifort-icc/tests/charm++/megatest \
  && make pgm \
  && cd /namd \
  && ./config Linux-x86_64-icc --with-mkl --charm-arch mpi-linux-x86_64-ifort-icc \
  && cd Linux-x86_64-icc \
  && make -j ${JOBS}

###############################################################################
# Final stage
###############################################################################
FROM mandeepkumar/compiler:intel-oneapi-2021.1-ubuntu18.04

# Copy build namd 
COPY --from=builder /namd /namd
ENV PATH=$PATH:/namd/Linux-x86_64-icc

# Setup labels
LABEL namd.version="${NAMD_VERSION}"
