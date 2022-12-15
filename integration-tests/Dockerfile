FROM nvidia/cuda:11.3.1-cudnn8-devel-ubuntu20.04

# Remove any third-party apt sources to avoid issues with expiring keys.
RUN rm -f /etc/apt/sources.list.d/*.list

# Install some basic utilities & python prerequisites
ARG DEBIAN_FRONTEND=noninteractive
RUN apt-get update -y && apt-get install -y --no-install-recommends\
    vim \
    curl \
    apt-utils \
    ssh \
    tree \
    ca-certificates \
    sudo \
    git \
    bzip2 \
    libx11-6 \
    make \
    build-essential \
    libssl-dev \
    zlib1g-dev \
    libbz2-dev \
    libreadline-dev \
    libsqlite3-dev \
    wget \
    llvm \
    libncursesw5-dev \
    xz-utils \
    tk-dev \
    libxml2-dev \
    libxmlsec1-dev \
    libffi-dev \
    liblzma-dev \
    python3-openssl && \
    rm -rf /var/lib/apt/lists/*

# Remove any third-party apt sources to avoid issues with expiring keys.
RUN rm -f /etc/apt/sources.list.d/*.list

# Set up python environment with pyenv
ARG PYTHON_VERSION 3.10.6
RUN curl https://pyenv.run | bash
ENV PYENV_ROOT="$HOME/.pyenv"
ENV PATH "$PYENV_ROOT/bin:$PYENV_ROOT/shims:$PATH"
ENV eval "$(pyenv init -)"
RUN cd $HOME && /bin/bash -c "source .bashrc" && \
    /bin/bash -c "pyenv install -v $PYTHON_VERSION" && \
    /bin/bash -c "pyenv global $PYTHON_VERSION"
    
# Install Poetry
ENV PATH "$HOME/.local/bin:$PATH"
ENV PYTHON_KEYRING_BACKEND keyring.backends.null.Keyring
RUN curl -sSL https://install.python-poetry.org | python - && \
    poetry config virtualenvs.in-project true && \ 
    poetry config virtualenvs.path "./.venv"
    
# [option] Set up DL development environment (with poetry)
RUN mkdir $HOME/workspace/machine-learning
WORKDIR $HOME/workspace/machine-learning
COPY pyproject.toml $HOME/workspace/machine-learning/pyproject.toml
RUN /bin/bash -c "pyenv local $PYTHON_VERSION" && \
    poetry env use python3 && \
    poetry run poetry install --no-cache
    
# Kill all the things we don't need
RUN find /etc/systemd/system \
    /lib/systemd/system \
    -path '*.wants/*' \
    -not -name '*journald*' \
    -not -name '*systemd-tmpfiles*' \
    -not -name '*systemd-user-sessions*' \
    -exec rm \{} \;

RUN mkdir -p /etc/sudoers.d

RUN systemctl set-default multi-user.target

STOPSIGNAL SIGRTMIN+3

# Uncomment these lines for a development install
#ENV TLJH_BOOTSTRAP_DEV=yes
#ENV TLJH_BOOTSTRAP_PIP_SPEC=/srv/src
#ENV PATH=/opt/tljh/hub/bin:${PATH}

CMD ["/bin/bash", "-c", "exec /lib/systemd/systemd --log-target=journal 3>&1"]    
