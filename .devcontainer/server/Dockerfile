ARG VARIANT=21-jdk
FROM eclipse-temurin:${VARIANT}

RUN apt-get update \
    && apt-get -y install --no-install-recommends \
    ca-certificates \
    git \
    nano \
    vim-tiny \
    && apt-get auto-remove -y \
    && apt-get clean -y \
    && chsh -s $(which bash) ubuntu \
    && echo 'export PS1="\e[01;32m\u\e[m:\e[01;34m\w\e[m\$ "' >> /home/ubuntu/.bashrc

ENV MAVEN_HOME=/opt/maven
ENV PATH=$MAVEN_HOME/bin:$PATH

ARG MAVEN_VERSION=3.9.11
ARG BASE_URL=https://dlcdn.apache.org/maven/maven-3/${MAVEN_VERSION}/binaries

RUN mkdir -p $MAVEN_HOME \
 && curl -fsSL -o /tmp/apache-maven.tar.gz ${BASE_URL}/apache-maven-${MAVEN_VERSION}-bin.tar.gz \
 && tar -xzf /tmp/apache-maven.tar.gz -C $MAVEN_HOME --strip-components=1 \
 && rm -f /tmp/apache-maven.tar.gz

# Set the default user
USER ubuntu

# Set the working directory
WORKDIR /home/ubuntu
