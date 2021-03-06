FROM ubuntu:16.04

WORKDIR /

# Update source
RUN apt-get update -y

# Basic tools
RUN apt-get install -y --no-install-recommends \
    apt-utils \
    build-essential \
    cmake \
    curl \
    git \
    libcurl3-dev \
    libgoogle-glog-dev \
    libfreetype6-dev \
    libpng12-dev \
    libprotobuf-dev \
    libzmq3-dev \
    pkg-config \
    python-dev \
    python-pip \
    protobuf-compiler \
    rsync \
    software-properties-common \
    unzip \
    zip \
    zlib1g-dev \
    openjdk-8-jdk \
    openjdk-8-jre-headless \
    openssh-server \
    wget \
    bsdmainutils
RUN pip install --upgrade pip

# Setup vim
RUN apt-get install -y --no-install-recommends \
    locales \
    vim

RUN locale-gen en_US.UTF-8
ENV LC_CTYPE=en_US.UTF-8
ENV LC_ALL=en_US.UTF-8
ENV TERM xterm-256color

# Set up Bazel.
# Running bazel inside a `docker build` command causes trouble, cf:
#   https://github.com/bazelbuild/bazel/issues/134
# The easiest solution is to set up a bazelrc file forcing --batch.
RUN echo "startup --batch" >>/etc/bazel.bazelrc
# Similarly, we need to workaround sandboxing issues:
#   https://github.com/bazelbuild/bazel/issues/418
RUN echo "build --spawn_strategy=standalone --genrule_strategy=standalone" \
    >>/etc/bazel.bazelrc
# Install the most recent bazel release.
ENV BAZEL_VERSION 0.13.1
RUN mkdir /tmp/bazel && \
    cd /tmp/bazel && \
    wget https://github.com/bazelbuild/bazel/releases/download/$BAZEL_VERSION/bazel-$BAZEL_VERSION-installer-linux-x86_64.sh && \
    chmod +x bazel-*.sh && \
    ./bazel-$BAZEL_VERSION-installer-linux-x86_64.sh && \
    cd / && \
    rm -rf /tmp/bazel

# Download NDK
RUN cd /opt/ && \
    wget -q https://dl.google.com/android/repository/android-ndk-r15c-linux-x86_64.zip && \
    unzip -q android-ndk-r15c-linux-x86_64.zip && \
    rm -f android-ndk-r15c-linux-x86_64.zip

ENV ANDROID_NDK_VERSION r15c
ENV ANDROID_NDK /opt/android-ndk-${ANDROID_NDK_VERSION}
ENV ANDROID_NDK_HOME ${ANDROID_NDK}

# add to PATH
ENV PATH ${PATH}:${ANDROID_NDK_HOME}

# Install tools
RUN apt-get install -y --no-install-recommends \
    android-tools-adb
RUN pip install -i http://pypi.douban.com/simple/ --trusted-host pypi.douban.com setuptools
RUN pip install -i http://pypi.douban.com/simple/ --trusted-host pypi.douban.com \
    tensorflow==1.8.0 \
    "numpy>=1.14.0" \
    scipy \
    jinja2 \
    pyyaml \
    sh==1.12.14 \
    pycodestyle==2.4.0 \
    filelock
