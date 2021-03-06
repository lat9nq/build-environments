FROM ubuntu:18.04
MAINTAINER yuzu

ENV CMAKE_VER=3.16.3
ENV GCC_VER=10
ENV QT_PKG_VER=512
ENV QT_VER=5.12.6
ENV UBUNTU_VER=bionic

# Create a user account yuzu (UID 1027) that the container will run as
RUN useradd -m -u 1027 -s /bin/bash yuzu && \
    DEBIAN_FRONTEND=noninteractive apt-get update && apt-get -y full-upgrade && \
    DEBIAN_FRONTEND=noninteractive apt-get install --no-install-recommends -y \
    build-essential \
    ccache \
    file \
    git \
    liblz4-dev \
    libopus-dev \
    libssl-dev \
    libzip-dev \
    libzstd-dev \
    nasm \
    ninja-build \
    python3-pip \
    python3-setuptools \
    software-properties-common \
    unzip \
    wget \
    zlib1g-dev \
    zsync && \
    pip3 install conan
# Install updated versions of FFmpeg, GCC, Qt, and SDL2 from launchpad repositories
RUN add-apt-repository -y ppa:beineri/opt-qt-${QT_VER}-${UBUNTU_VER} && \
    add-apt-repository -y ppa:cybermax-dexter/sdl2-backport && \
    add-apt-repository -y ppa:jonathonf/ffmpeg-4 && \
    add-apt-repository -y ppa:ubuntu-toolchain-r/test && \
    apt-get update -y && \
    DEBIAN_FRONTEND=noninteractive apt-get install --no-install-recommends -y \
    g++-${GCC_VER} \
    gcc-${GCC_VER} \
    libavcodec-dev \
    libavutil-dev \
    libsdl2-dev \
    libswscale-dev \
    qt${QT_PKG_VER}base \
    qt${QT_PKG_VER}tools \
    qt${QT_PKG_VER}webengine && \
    update-alternatives --install /usr/bin/gcc gcc /usr/bin/gcc-${GCC_VER} ${GCC_VER} && \
    update-alternatives --install /usr/bin/g++ g++ /usr/bin/g++-${GCC_VER} ${GCC_VER} && \
    apt-get clean autoclean && \
    apt-get autoremove --yes && \
    rm -rf /var/lib/apt /var/lib/dpkg /var/lib/cache /var/lib/log
# Install glslangValidator from upstream
# glslangValidator is not available from Ubuntu's Bionic repositories.
RUN cd /tmp && \
    wget https://github.com/KhronosGroup/glslang/releases/download/master-tot/glslang-master-linux-Release.zip && \
    unzip glslang-master-linux-Release.zip -d /usr && \
    rm -v glslang-master-linux-Release.zip
# Install CMake from upstream
# yuzu requires CMake version 3.15, however Ubuntu only provides 3.10 to Bionic.
RUN cd /tmp && \
    wget https://github.com/Kitware/CMake/releases/download/v${CMAKE_VER}/cmake-${CMAKE_VER}-Linux-x86_64.tar.gz && \
    tar xvf cmake-${CMAKE_VER}-Linux-x86_64.tar.gz && \
    cp -rv cmake-${CMAKE_VER}-Linux-x86_64/* /usr && \
    rm -rf cmake-*
# Setup paths for Qt binaries
ENV LD_LIBRARY_PATH=/opt/qt${QT_PKG_VER}/lib:${LD_LIBRARY_PATH}
ENV PATH=/opt/qt${QT_PKG_VER}/bin:${PATH}
USER 1027
RUN conan install boost/1.73.0@ -s compiler.libcxx=libstdc++11 --build=missing && \
    conan install catch2/2.13.0@ -s compiler.libcxx=libstdc++11 --build=missing && \
    conan install fmt/7.1.2@ -s compiler.libcxx=libstdc++11 --build=missing && \
    conan install nlohmann_json/3.9.1@ -s compiler.libcxx=libstdc++11 --build=missing
