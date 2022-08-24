###############################################################################
# Ubuntu 18.04, and VMD 1.9.3
#
# Build with,
# docker build -t vmd:1.9.3-intel-ubuntu18.04 -f ./Dockerfile
#
# VMD Reference,
# W. Humphrey, A. Dalke and K. Schulten, "VMD: Visual Molecular Dynamics,” Journal of Molecular Graphics, 1996, vol. 14, pp. 33-38.
###############################################################################
FROM ubuntu:18.04

RUN apt-get update &&\
    apt-get install xorg xauth curl vim make -y

# Update according to https://www.ks.uiuc.edu/Research/vmd
RUN curl -o vmd-1.9.3.tar.gz https://www.ks.uiuc.edu/Research/vmd/vmd-1.9.3/files/final/vmd-1.9.3.bin.LINUXAMD64-CUDA8-OptiX4-OSPRay111p1.opengl.tar.gz &&\
    tar -xf vmd-1.9.3.tar.gz &&\
    cd vmd-1.9.3 &&\
    ./configure LINUXAMD64 &&\
    cd src &&\
    make install 
