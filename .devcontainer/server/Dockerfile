FROM debian:trixie

RUN groupadd debian \
      --gid 1000 \
    && useradd debian \
      --uid 1000 \
      --gid debian \
      --shell /bin/bash \
      --create-home

RUN apt-get update \
    && apt-get -y install --no-install-recommends \
    ca-certificates \
    curl \
    dirmngr \
    git \
    gnupg \
    gpg \
    gpg-agent \
    libatomic1 \
    nano \
    python3 \
    unzip \
    vim-tiny \
    wget \
    xz-utils \
    && apt-get auto-remove -y \
    && apt-get clean -y \
    && rm -rf /var/lib/apt/lists/* \
    && echo 'export PS1="\e[01;32m\u\e[m:\e[01;34m\w\e[m\$ "' >> /home/debian/.bashrc

ENV JAVA_HOME=/opt/java/openjdk
COPY --from=eclipse-temurin:21 $JAVA_HOME $JAVA_HOME
ENV PATH="${JAVA_HOME}/bin:${PATH}"

ENV MAVEN_HOME=/opt/maven
ENV PATH=$MAVEN_HOME/bin:$PATH

ARG MAVEN_VERSION=3.9.11
ARG BASE_URL=https://dlcdn.apache.org/maven/maven-3/${MAVEN_VERSION}/binaries

RUN mkdir -p $MAVEN_HOME \
 && curl -fsSL -o /tmp/apache-maven.tar.gz ${BASE_URL}/apache-maven-${MAVEN_VERSION}-bin.tar.gz \
 && tar -xzf /tmp/apache-maven.tar.gz -C $MAVEN_HOME --strip-components=1 \
 && rm -f /tmp/apache-maven.tar.gz

ARG NODE_VERSION=22.20.0
RUN ARCH= OPENSSL_ARCH= && dpkgArch="$(dpkg --print-architecture)" \
    && case "${dpkgArch##*-}" in \
      amd64) ARCH='x64' OPENSSL_ARCH='linux-x86_64';; \
      ppc64el) ARCH='ppc64le' OPENSSL_ARCH='linux-ppc64le';; \
      s390x) ARCH='s390x' OPENSSL_ARCH='linux*-s390x';; \
      arm64) ARCH='arm64' OPENSSL_ARCH='linux-aarch64';; \
      armhf) ARCH='armv7l' OPENSSL_ARCH='linux-armv4';; \
      i386) ARCH='x86' OPENSSL_ARCH='linux-elf';; \
      *) echo "unsupported architecture"; exit 1 ;; \
    esac \
    && set -ex \
    # use pre-existing gpg directory, see https://github.com/nodejs/docker-node/pull/1895#issuecomment-1550389150
    && export GNUPGHOME="$(mktemp -d)" \
    # gpg keys listed at https://github.com/nodejs/node#release-keys
    && for key in \
      C0D6248439F1D5604AAFFB4021D900FFDB233756 \
      DD792F5973C6DE52C432CBDAC77ABFA00DDBF2B7 \
      CC68F5A3106FF448322E48ED27F5E38D5B0A215F \
      8FCCA13FEF1D0C2E91008E09770F7A9A5AE15600 \
      890C08DB8579162FEE0DF9DB8BEAB4DFCF555EF4 \
      C82FA3AE1CBEDC6BE46B9360C43CEC45C17AB93C \
      108F52B48DB57BB0CC439B2997B01419BD92F80A \
      A363A499291CBBC940DD62E41F10027AF002F8B0 \
    ; do \
      gpg --batch --keyserver keyserver.ubuntu.com --recv-keys "$key" || \
      gpg --batch --keyserver hkps://keys.openpgp.org --recv-keys "$key" ; \
    done \
    && curl -fsSLO --compressed "https://nodejs.org/dist/v$NODE_VERSION/node-v$NODE_VERSION-linux-$ARCH.tar.xz" \
    && curl -fsSLO --compressed "https://nodejs.org/dist/v$NODE_VERSION/SHASUMS256.txt.asc" \
    && gpg --batch --decrypt --output SHASUMS256.txt SHASUMS256.txt.asc \
    && gpgconf --kill all \
    && rm -rf "$GNUPGHOME" \
    && grep " node-v$NODE_VERSION-linux-$ARCH.tar.xz\$" SHASUMS256.txt | sha256sum -c - \
    && tar -xJf "node-v$NODE_VERSION-linux-$ARCH.tar.xz" -C /usr/local --strip-components=1 --no-same-owner \
    && rm "node-v$NODE_VERSION-linux-$ARCH.tar.xz" SHASUMS256.txt.asc SHASUMS256.txt \
    # Remove unused OpenSSL headers to save ~34MB. See this NodeJS issue: https://github.com/nodejs/node/issues/46451
    && find /usr/local/include/node/openssl/archs -mindepth 1 -maxdepth 1 ! -name "$OPENSSL_ARCH" -exec rm -rf {} \; \
    && npm install -g npm \
    && npm install -g @anthropic-ai/claude-code \
    # smoke tests
    && node --version \
    && npm --version \
    && rm -rf /tmp/*

ARG BUN_VERSION=1.2.22
RUN arch="$(dpkg --print-architecture)" \
    && case "${arch##*-}" in \
      amd64) build="x64-baseline";; \
      arm64) build="aarch64";; \
      *) echo "error: unsupported architecture: $arch"; exit 1 ;; \
    esac \
    && version="$BUN_VERSION" \
    && case "$version" in \
      latest | canary | bun-v*) tag="$version"; ;; \
      v*)                       tag="bun-$version"; ;; \
      *)                        tag="bun-v$version"; ;; \
    esac \
    && case "$tag" in \
      latest) release="latest/download"; ;; \
      *)      release="download/$tag"; ;; \
    esac \
    && curl "https://github.com/oven-sh/bun/releases/$release/bun-linux-$build.zip" \
      -fsSLO \
      --compressed \
      --retry 5 \
      || (echo "error: failed to download: $tag" && exit 1) \
    && for key in \
      "F3DCC08A8572C0749B3E18888EAB4D40A7B22B59" \
    ; do \
      gpg --batch --keyserver hkps://keys.openpgp.org --recv-keys "$key" \
      || gpg --batch --keyserver keyserver.ubuntu.com --recv-keys "$key" ; \
    done \
    && curl "https://github.com/oven-sh/bun/releases/$release/SHASUMS256.txt.asc" \
      -fsSLO \
      --compressed \
      --retry 5 \
    && gpg --batch --decrypt --output SHASUMS256.txt SHASUMS256.txt.asc \
      || (echo "error: failed to verify: $tag" && exit 1) \
    && grep " bun-linux-$build.zip\$" SHASUMS256.txt | sha256sum -c - \
      || (echo "error: failed to verify: $tag" && exit 1) \
    && unzip "bun-linux-$build.zip" \
    && mv "bun-linux-$build/bun" /usr/local/bin/bun \
    && rm -f "bun-linux-$build.zip" SHASUMS256.txt.asc SHASUMS256.txt \
    && chmod +x /usr/local/bin/bun

# Set the default user
USER debian

# Set the working directory
WORKDIR /home/debian
