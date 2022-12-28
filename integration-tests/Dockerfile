# Dockerfile

# Pull bse image
# - 저는 cuda 10.2에 ubuntu 18.04를 사용하고 있기 때문에 아래의 이미지를 가지고 왔습니다.
# - docker hub에서 nvidia official image를 확인하시고 상황에 맞는 이미지를 가지고 오시면 되겠습니다.
FROM nvidia/cuda:10.2-base-ubuntu18.04
ENV LC_ALL=C.UTF-8

# Install basic utilities
# - ubuntu에 필요한 기본적인 것들을 설치해줍니다.
# - 더 원하시는 util이 있으시면 아래 라인에 추가 혹은 삭제 해주시면 됩니다.
RUN . /etc/os-release; \
                printf "deb http://ppa.launchpad.net/jonathonf/vim/ubuntu %s main" "$UBUNTU_CODENAME" main | tee /etc/apt/sources.list.d/vim-ppa.list && \
                apt-key  adv --keyserver hkps://keyserver.ubuntu.com --recv-key 4AB0F789CBA31744CC7DA76A8CF63AD3F06FC659 && \
                apt-get update --fix-missing && \
                env DEBIAN_FRONTEND=noninteractive apt-get dist-upgrade --autoremove --purge --no-install-recommends -y \
                        build-essential \
                        bzip2 \
                        ca-certificates \
                        curl \
                        git \
                        libcanberra-gtk-module \
                        libgtk2.0-0 \
                        libx11-6 \
                        sudo \
                        graphviz \
                        vim-nox

# Install miniconda
# - python에서 ML/DL을 사용할 것이기 때문에 여러모로 편리한 miniconda를 설치합니다.
# - 환경변수도 함께 지정해줍니다.
ENV LANG=C.UTF-8 LC_ALL=C.UTF-8
ENV PATH /opt/conda/bin:$PATH
RUN apt-get install -y wget bzip2 ca-certificates \
    libglib2.0-0 libxext6 libsm6 libxrender1 \
    git mercurial subversion
RUN wget --quiet https://repo.continuum.io/miniconda/Miniconda3-latest-Linux-x86_64.sh -O ~/miniconda.sh && \
    /bin/bash ~/miniconda.sh -b -p /opt/conda && \
    rm ~/miniconda.sh && \
    ln -s /opt/conda/etc/profile.d/conda.sh /etc/profile.d/conda.sh && \
    echo ". /opt/conda/etc/profile.d/conda.sh" >> ~/.bashrc && \
    echo "conda activate base" >> ~/.bashrc
RUN apt-get install -y curl grep sed dpkg && \
    TINI_VERSION=`curl https://github.com/krallin/tini/releases/latest | grep -o "/v.*\"" | sed 's:^..\(.*\).$:\1:'` && \
    curl -L "https://github.com/krallin/tini/releases/download/v${TINI_VERSION}/tini_${TINI_VERSION}.deb" > tini.deb && \
    dpkg -i tini.deb && \
    rm tini.deb && \
    apt-get clean

# Install python packages
# - python에서 사용할 패키지를 설치합니다.
RUN pip install torch torchvision && \
    pip install cython && \
    pip install simplejson && \
    conda install av -c conda-forge && \
    pip install jupyterlab jupyterhub
