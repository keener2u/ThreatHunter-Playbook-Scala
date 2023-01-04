FROM mcr.microsoft.com/devcontainers/universal:2.1.1

RUN curl -fLo cs https://git.io/coursier-cli-linux &&\
    chmod +x cs &&\
    ./cs java --jvm adopt:1.8.0-252 --env >> .env &&\
    ./cs install --env >> .env &&\
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
