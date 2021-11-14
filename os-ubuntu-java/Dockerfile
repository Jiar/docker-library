FROM ubuntu:20.04

LABEL maintainer="Jiar <iiimjiar@gmail.com>"
LABEL description="ubuntu deploy java"

ENV DEBIAN_FRONTEND noninteractive

RUN apt-get update -y

# time
RUN apt-get install -y systemd

# java
RUN apt-get install -y software-properties-common \
  && add-apt-repository ppa:openjdk-r/ppa \
  && apt-get install -y openjdk-8-jdk \
  && rm -rf /var/lib/apt/lists/*

ENV JAVA_HOME /usr/lib/jvm/java-8-openjdk-amd64