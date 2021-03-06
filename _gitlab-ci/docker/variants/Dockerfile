#default args
ARG BASE_IMAGE=base_ubuntu_srsgui
ARG INCLUDE_UHD=false
ARG INCLUDE_LIMESDR=false
ARG INCLUDE_CMNALIB=false
ARG INCLUDE_SRSLTE=true

FROM $BASE_IMAGE as base

  
# UHD (This is installed to satisfy condition RF_FOUND for srsLTE, any other supported RF frontend drivers should be good as well)
ARG INCLUDE_UHD
WORKDIR / 
RUN if [ "$INCLUDE_UHD" = true ]; then \
  add-apt-repository ppa:ettusresearch/uhd \
  && apt-get update \
  && DEBIAN_FRONTEND=noninteractive apt-get install -y libuhd-dev libuhd003 uhd-host \
  ; fi

# LimeSDR
#ARG INCLUDE_LIMESDR
#WORKDIR / 
#RUN if [ "$INCLUDE_LIMESDR" = true ]; then \
#  ...
#  ; fi


# CMNALIB
ARG INCLUDE_CMNALIB
WORKDIR / 
RUN if [ "$INCLUDE_CMNALIB" = true ]; then \
  apt-get update \
  && apt-get install -y libglib2.0-dev libudev-dev libcurl4-gnutls-dev \
  && git clone --branch falcon_v0.1 https://github.com/falkenber9/c-mnalib.git \
  && mkdir /c-mnalib/build \
  && cd /c-mnalib/build \
  && cmake ../ \
  && make -j install \
  ; fi

# srsLTE (AVX and other accelerations are explicitly switched off. Otherwise rounding inaccuracies cause test cases to fail (if accelerated Viterbi decoder is involved in the test))
#WORKDIR /
#RUN git clone --branch falcon_v0.1 https://github.com/falkenber9/srsLTE.git && echo 1 && pwd
#WORKDIR /srsLTE/build
#RUN cmake -DENABLE_AVX=OFF -DENABLE_AVX2=OFF -DENABLE_FMA=OFF -DENABLE_AVX512=OFF  ../ \
#  && make -j install
ARG INCLUDE_SRSLTE
WORKDIR /
RUN if [ "$INCLUDE_SRSLTE" = true ]; then \
  git clone --branch falcon_v0.1 https://github.com/falkenber9/srsLTE.git && echo 1 && pwd \
  && mkdir srsLTE/build \
  && cd srsLTE/build \
  && cmake -DENABLE_AVX=OFF -DENABLE_AVX2=OFF -DENABLE_FMA=OFF -DENABLE_AVX512=OFF  ../ \
  && make -j install \
  ; fi

# FALCON dependencies
RUN apt-get update && apt-get install -y libglib2.0-dev libudev-dev libcurl4-gnutls-dev libboost-all-dev qtdeclarative5-dev libqt5charts5-dev

# FALCON	(moved into the test script, since download+build process is part of the CI test)
#WORKDIR / 
#RUN git clone https://github.com/falkenber9/falcon.git
#WORKDIR /falcon/build
#RUN cmake ../ \
#  && make -j


ENTRYPOINT [ "stdbuf", "-o", "L" ]
