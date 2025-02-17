# base image
ARG BASE_IMAGE=ubuntu:22.04
FROM ${BASE_IMAGE} as base

LABEL maintainer="iamsimmk@gmail.com"
LABEL description="Opencv installed ubuntu image"

ARG OPENCV_VERSION=4.6.0
ARG CMAKE_VERSION=3.20.4

ENV DEBIAN_FRONTEND=noninteractive

# change apt server
RUN sed -i 's/kr.archive.ubuntu.com/mirror.kakao.com/g' /etc/apt/sources.list
RUN sed -i 's/security.ubuntu.com/mirror.kakao.com/g' /etc/apt/sources.list
RUN sed -i 's/extras.ubuntu.com/mirror.kakao.com/g' /etc/apt/sources.list



# install dependency
WORKDIR /dependencies
RUN apt-get update && apt-get install -y --no-install-recommends \
        pkg-config \
        wget udev sudo \
        libusb-1.0-0-dev \
        build-essential \
        libboost-all-dev \
        unzip \
        libjpeg-dev libpng-dev \
        qtmultimedia5-dev libqt5multimedia5 libqt5multimedia5-plugins \
        apt-utils \
        x11-apps \
        fonts-nanum \
        libtbb-dev \
        gdb vim git

# Cmake
ARG CMAKE_INSTALL_SCRIPT=cmake-${CMAKE_VERSION}-linux-x86_64.sh
RUN cd /tmp && \
    wget --no-check-certificate https://github.com/Kitware/CMake/releases/download/v${CMAKE_VERSION}/${CMAKE_INSTALL_SCRIPT} && \
    chmod +x ${CMAKE_INSTALL_SCRIPT} && \
    ./${CMAKE_INSTALL_SCRIPT} --prefix=/usr/local --exclude-subdir --skip-license && \
    rm ./${CMAKE_INSTALL_SCRIPT}

# Opencv
WORKDIR /tmp/opencv_build
RUN wget --no-check-certificate https://github.com/opencv/opencv/archive/refs/tags/${OPENCV_VERSION}.zip && \
    wget -O opencv_contrib-${OPENCV_VERSION}.zip --no-check-certificate https://github.com/opencv/opencv_contrib/archive/refs/tags/${OPENCV_VERSION}.zip && \
    unzip ${OPENCV_VERSION}.zip && \
    unzip opencv_contrib-${OPENCV_VERSION}.zip && \
    rm ${OPENCV_VERSION}.zip && cd opencv-${OPENCV_VERSION} && \
    mkdir build && cd build && \
    cmake \
        -DCMAKE_BUILD_TYPE=Release \
        -DCMAKE_INSTALL_PREFIX=/usr/local \
        -DBUILD_DOCS=OFF \
        -DBUILD_TESTS=OFF \
        -DBUILD_PERF_TESTS=OFF \
        -DBUILD-EXAMPLES=OFF \
        -DBUILD_opencv_apps=OFF \
        -DENABLE_PRECOMPILED_HEADERS=OFF \
        -DENABLE_CXX11=ON \
        -DOPENCV_GENERATE_PKGCONFIG=ON \
        -DOPENCV_EXTRA_MODULES_PATH=/tmp/opencv_build/opencv_contrib-${OPENCV_VERSION}/modules \
        -DWITH_GTK=OFF \
        -DWITH_QT=ON .. && \
    make -j"$(nproc)" && make install && \
    sh -c 'echo '/usr/local/lib' > /etc/ld.so.conf.d/opencv.conf' && \
    ldconfig && \
    rm -rf /tmp/opencv_build
