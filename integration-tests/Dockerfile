# Systemd inside a Docker container, for CI only
FROM ufoym/deepo

# DEBIAN_FRONTEND is set to avoid being asked for input and hang during build:
# https://anonoz.github.io/tech/2020/04/24/docker-build-stuck-tzdata.html
RUN export DEBIAN_FRONTEND=noninteractive \
 && apt-get update \
 && apt-get upgrade -y \
 && apt-get install --yes \
        systemd \
        curl \
        git \
        sudo \
    openjdk-8-jdk wget curl git python3-dev \
    language-pack-ko \
 && rm -rf /var/lib/apt/lists/*

RUN locale-gen en_US.UTF-8 && \
    update-locale LANG=en_US.UTF-8

RUN find /etc/systemd/system \
    /lib/systemd/system \
    -path '*.wants/*' \
    -not -name '*journald*' \
    -not -name '*systemd-tmpfiles*' \
    -not -name '*systemd-user-sessions*' \
    -exec rm \{} \;

# Install another packages
RUN pip install -e git+https://github.com/kanth989/pandas_explode#egg=pandas_explode
RUN pip install \
    autopep8 pytorch_pretrained_bert \
    s3fs fastparquet soynlp konlpy \
    randomcolor pynamodb plotly
RUN pip install "dask[complete]"
RUN pip install python-snappy

# Add Mecab-Ko
RUN curl -L https://raw.githubusercontent.com/konlpy/konlpy/master/scripts/mecab.sh | bash

RUN mkdir -p /etc/sudoers.d

RUN systemctl set-default multi-user.target

STOPSIGNAL SIGRTMIN+3

# Uncomment these lines for a development install
#ENV TLJH_BOOTSTRAP_DEV=yes
#ENV TLJH_BOOTSTRAP_PIP_SPEC=/srv/src
#ENV PATH=/opt/tljh/hub/bin:${PATH}

CMD ["/bin/bash", "-c", "exec /lib/systemd/systemd --log-target=journal 3>&1"]
