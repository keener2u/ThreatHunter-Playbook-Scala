FROM mcr.microsoft.com/devcontainers/universal:2.1.1

RUN curl -Lo coursier https://git.io/coursier-cli
RUN chmod +x coursier
RUN ./coursier launch --fork almond -- --install
RUN rm -f coursier

RUN pip install jupyterlab==2.1.5 jupyterlab-server==1.1.5
