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
FROM mcr.microsoft.com/vscode/devcontainers/java:17-bullseye

COPY --from=download-and-extract-stage /usr/local/scala3 /usr/local/scala3
ENV SCALA_HOME=/usr/local/scala3

COPY --from=download-and-extract-stage /usr/local/sbt /usr/local/sbt
ENV SBT_HOME=/usr/local/sbt

ENV PATH="${SCALA_HOME}/bin:${SBT_HOME}/bin:${PATH}"

USER ${NB_UID}

# Install Jupyter Notebook, Lab, and Hub
# Generate a notebook server config
# Cleanup temporary files
# Correct permissions
# Do all this in a single RUN command to avoid duplicating all of the
# files across image layers when the permissions change
WORKDIR /tmp
RUN mamba install --quiet --yes \
    'notebook' \
    'jupyterhub' \
    'jupyterlab' && \
    jupyter notebook --generate-config && \
    mamba clean --all -f -y && \
    npm cache clean --force && \
    jupyter lab clean && \
    rm -rf "/home/${NB_USER}/.cache/yarn" && \
    fix-permissions "${CONDA_DIR}" && \
    fix-permissions "/home/${NB_USER}"

EXPOSE 8888

# Configure container startup
CMD ["start-notebook.sh"]

# Copy local files as late as possible to avoid cache busting
COPY start-notebook.sh start-singleuser.sh /usr/local/bin/
# Currently need to have both jupyter_notebook_config and jupyter_server_config to support classic and lab
COPY jupyter_server_config.py /etc/jupyter/

# Fix permissions on /etc/jupyter as root
USER root

# Legacy for Jupyter Notebook Server, see: [#1205](https://github.com/jupyter/docker-stacks/issues/1205)
RUN sed -re "s/c.ServerApp/c.NotebookApp/g" \
    /etc/jupyter/jupyter_server_config.py > /etc/jupyter/jupyter_notebook_config.py && \
    fix-permissions /etc/jupyter/

# HEALTHCHECK documentation: https://docs.docker.com/engine/reference/builder/#healthcheck
# This healtcheck works well for `lab`, `notebook`, `nbclassic`, `server` and `retro` jupyter commands
# https://github.com/jupyter/docker-stacks/issues/915#issuecomment-1068528799
HEALTHCHECK  --interval=5s --timeout=3s --start-period=5s --retries=3 \
    CMD wget -O- --no-verbose --tries=1 --no-check-certificate \
    http${GEN_CERT:+s}://localhost:8888${JUPYTERHUB_SERVICE_PREFIX:-/}api || exit 1

# Switch back to jovyan to avoid accidental container runs as root
USER ${NB_UID}

WORKDIR "${HOME}"