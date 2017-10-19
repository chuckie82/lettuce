FROM ubuntu:16.04
MAINTAINER Haoyuan Li <hyli16@stanford.edu>

# Prepare the environment
RUN apt-get -q update \
    && apt-get -qy install  gfortran  g++  gcc  make  cmake  curl  wget apt-utils nano emacs git \
    && apt-get install -y build-essential libgl1-mesa-glx \
    && apt-get install -y libmpich-dev \
    && mkdir /home/user \
    && mkdir /home/user/scalapack \
    && useradd -r -g users user \
    && chown user:users -R /home/user

# Move to this new position
WORKDIR /home/user
USER user

# Install anaconda to use python
RUN wget https://repo.continuum.io/archive/Anaconda2-5.0.0.1-Linux-x86_64.sh \
    && bash Anaconda2-5.0.0.1-Linux-x86_64.sh -b \
    && rm Anaconda2-5.0.0.1-Linux-x86_64.sh

# Install Scalapack
USER root
ENV PATH "/home/user/anaconda2/bin:$PATH"
WORKDIR /home/user/scalapack

RUN wget http://www.netlib.org/scalapack/scalapack_installer.tgz \
    && tar -zxf scalapack_installer.tgz \
    && rm scalapack_installer.tgz \
    && python scalapack_installer/setup.py --downall --mpiincdir=/usr/lib/mpich/include

# Install Elemental
WORKDIR /home/user
ENV LD_LIBRARY_PATH "/usr/lib:/usr/local/lib:$LD_LIBRARY_PATH"
ENV PATH "/usr/lib:/usr/local/lib:/usr/local/bin:$PATH"

RUN git clone https://github.com/elemental/Elemental \
    && mkdir /home/user/Elemental/build

WORKDIR /home/user/Elemental/build
RUN cmake -D EL_DISABLE_SCALAPACK=OFF .. \
    && make \
    && make install

ENV PYTHONPATH "/home/user/anaconda2/lib/python2.7/site-packages:/usr/local/python"
ENV LD_LIBRARY_PATH "/usr/local/python:$LD_LIBRARY_PATH"

USER user
WORKDIR /home/user

ENTRYPOINT ["/bin/bash"]
