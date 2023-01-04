FROM mcr.microsoft.com/devcontainers/universal:2.1.1

RUN curl -fLo cs https://git.io/coursier-cli-linux
RUN chmod +x cs
RUN ./cs install --env >> .env
RUN ./cs install ammonite:2.1.4
RUN ./cs install bloop
RUN ./cs install cs
RUN ./cs install sbt-launcher
RUN ./cs install scala:2.13.2
RUN ./cs install scalafmt:2.5.3
RUN ./cs fetch org.scalameta::metals:0.9.0 >/dev/null
RUN ./cs fetch org.scala-sbt:sbt:1.3.13 >/dev/null
RUN ./cs fetch coursier:2.0.0-RC5-3 >/dev/null
RUN rm -f cs

RUN pip install jupyterlab==2.1.5 jupyterlab-server==1.1.5
