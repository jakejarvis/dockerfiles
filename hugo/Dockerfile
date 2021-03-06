# Hugo doesn't require Go to run, *except* if you're using Hugo Modules. It's
# much easier to install Node on the Go base image than vice-versa.
FROM golang:1.14-alpine

LABEL maintainer="Jake Jarvis <jake@jarv.is>"

# the following version can be overridden at image build time with --build-arg
ARG HUGO_VERSION=0.74.3
# remove/comment the following line completely to build with vanilla Hugo:
ARG HUGO_EXTENDED=1

# only install libc6-compat & libstdc++ if we're building extended Hugo
# https://gitlab.com/yaegashi/hugo/commit/22f0d5cbd6114210ba7835468facbdee60609aa2
RUN apk update && \
    apk add --no-cache \
      ca-certificates \
      git \
      nodejs \
      npm \
      yarn \
      python3 \
      py3-pip \
      ruby \
      ${HUGO_EXTENDED:+libc6-compat libstdc++} && \
    update-ca-certificates && \
    npm install --global postcss-cli autoprefixer @babel/core @babel/cli && \
    pip3 install --upgrade Pygments==2.* && \
    gem install asciidoctor && \
    wget https://github.com/gohugoio/hugo/releases/download/v${HUGO_VERSION}/hugo_${HUGO_EXTENDED:+extended_}${HUGO_VERSION}_Linux-64bit.tar.gz && \
    wget https://github.com/gohugoio/hugo/releases/download/v${HUGO_VERSION}/hugo_${HUGO_VERSION}_checksums.txt && \
    grep hugo_${HUGO_EXTENDED:+extended_}${HUGO_VERSION}_Linux-64bit.tar.gz hugo_${HUGO_VERSION}_checksums.txt | sha256sum -c && \
    tar xf hugo_${HUGO_EXTENDED:+extended_}${HUGO_VERSION}_Linux-64bit.tar.gz && \
    mv ./hugo /usr/local/bin/ && \
    chmod +x /usr/local/bin/hugo && \
    rm -rf hugo_* LICENSE README.md

# verify everything's OK, exit otherwise
RUN hugo version && \
    hugo env && \
    postcss --version && \
    autoprefixer --version && \
    babel --version && \
    pygmentize -V && \
    asciidoctor --version

# add site source as volume
VOLUME /src
WORKDIR /src

# expose live-refresh server
EXPOSE 1313

ENTRYPOINT ["hugo"]
