ARG SCALA_VERSION=3.1.2
ARG SBT_VERSION=1.6.2

# -------------------------- STAGE 1 -----------------------------------------
# download-and-extract-stage does not neet to be based on debian:buster.
# As long as it has the `tar` command, it should work.
FROM debian:buster-slim as download-and-extract-stage
ARG SCALA_VERSION
ARG SBT_VERSION
# An example URL to download Scala: https://github.com/lampepfl/dotty/releases/download/3.0.0-M3/scala3-3.0.0-M3.tar.gz
ADD https://github.com/lampepfl/dotty/releases/download/${SCALA_VERSION}/scala3-${SCALA_VERSION}.tar.gz /tmp/scala3-${SCALA_VERSION}.tar.gz
RUN tar -xzf /tmp/scala3-${SCALA_VERSION}.tar.gz -C /usr/local && mv /usr/local/scala3-${SCALA_VERSION} /usr/local/scala3

# An example URL to download sbt: https://github.com/sbt/sbt/releases/download/v1.4.6/sbt-1.4.6.tgz
ADD https://github.com/sbt/sbt/releases/download/v${SBT_VERSION}/sbt-${SBT_VERSION}.tgz /tmp/sbt-${SBT_VERSION}.tar.gz
RUN tar -xzf /tmp/sbt-${SBT_VERSION}.tar.gz -C /usr/local

# -------------------------- STAGE 2 -----------------------------------------
# Install Scala and SBT
FROM mcr.microsoft.com/devcontainers/universal:2.1.1

RUN curl -fLo cs https://git.io/coursier-cli-linux &&\
    chmod +x cs &&\
    ./cs java --jvm adopt:1.8.0-252 --env >> /home/gitpod/.bashrc.d/90-cs &&\
    ./cs install --env >> /home/gitpod/.bashrc.d/90-cs &&\
    ./cs install \
      ammonite:2.1.4 \
      bloop \
      cs \
      sbt-launcher \
      scala:2.13.2 \
      scalafmt:2.5.3 &&\
    ./cs fetch org.scalameta::metals:0.9.0 >/dev/null &&\
    ./cs fetch org.scala-sbt:sbt:1.3.13 >/dev/null &&\
    ./cs fetch coursier:2.0.0-RC5-3 >/dev/null &&\
    rm -f cs

RUN pip install jupyterlab==2.1.5 jupyterlab-server==1.1.5
