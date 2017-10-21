FROM ubuntu:16.04
MAINTAINER Haoyuan Li <hyli16@stanford.edu>

# Prepare the environment
# Get a new user and qt and mpich
RUN apt-get -q update \
    && apt-get -qy install  gfortran  g++  gcc  make  cmake  curl  wget apt-utils nano emacs git perl \
    && apt-get install -qy debconf-utils software-properties-common \
    && apt-get install -y build-essential libgl1-mesa-glx mesa-common-dev libglu1-mesa-dev libglew-dev \
    && apt-get install -y libmpich-dev \
    && add-apt-repository --yes ppa:ubuntu-sdk-team/ppa \
    && apt-get update -qq \
    && apt-get install -y qtbase5-dev qtdeclarative5-dev libsqlite3-dev \
    && apt-get install -y qt5-default qttools5-dev-tools \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/* \
    && mkdir /home/user \
    && useradd -r -g users user \
    && chown user:users -R /home/user

# Move to this new position
WORKDIR /home/user
USER user

# Install anaconda to use python and download Elemental
RUN wget https://repo.continuum.io/archive/Anaconda2-5.0.0.1-Linux-x86_64.sh \
    && bash Anaconda2-5.0.0.1-Linux-x86_64.sh -b \
    && rm Anaconda2-5.0.0.1-Linux-x86_64.sh \
    && git clone https://github.com/elemental/Elemental \
    && mkdir /home/user/Elemental/build

# Install Elemental
USER root
ENV PATH "/home/user/anaconda2/bin:/usr/lib:/usr/local/lib:/usr/local/bin:$PATH"
ENV LD_LIBRARY_PATH "/usr/lib:/usr/local/lib:/usr/local/python:$LD_LIBRARY_PATH"
ENV PYTHONPATH "/home/user/anaconda2/lib/python2.7/site-packages:/usr/local/python"
WORKDIR /home/user/Elemental/build

RUN cmake -D EL_DISABLE_SCALAPACK=OFF .. \
    && make \
    && make install

# Fix a bug induced by the fact that there is no display device
ENV QT_QPA_PLATFORM offscreen

# Change user and Word directory
USER user
WORKDIR /home/user

ENTRYPOINT ["/bin/bash"]
