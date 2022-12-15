# Base image built from `tf.Dockerfile`

FROM tlkh/ai-lab:20.12-base

LABEL maintainer="Timothy Liu <timothy_liu@mymail.sutd.edu.sg>"

USER root

ENV DEBIAN_FRONTEND=noninteractive \
    TF_FORCE_GPU_ALLOW_GROWTH=true

# install conda TensorFlow and PyTorch

USER $NB_UID

RUN conda install -c anaconda -c pytorch --quiet --yes \
      'python=3.7' \
      pytorch torchvision torchaudio \
      tensorflow-gpu \
      tensorflow-hub \
      tensorflow-datasets \
      'cudatoolkit=10.2' && \
    pip install --no-cache-dir torchtext pytorch-lightning['extra'] && \
    pip uninstall pillow -y && \
      CC="cc -mavx2" pip install -U --force-reinstall --no-cache-dir pillow-simd && \
    conda clean -tipsy && \
    conda build purge-all && \
    find $CONDA_DIR -type f,l -name '*.a' -delete && \
    find $CONDA_DIR -type f,l -name '*.pyc' -delete && \
    find $CONDA_DIR -type f,l -name '*.js.map' -delete && \
    rm -rf $CONDA_DIR/pkgs && \
    rm -rf /tmp/* && \
    rm -rf $HOME/.cache && \
    rm -rf $HOME/.node-gyp && \
    fix-permissions $CONDA_DIR && \
    fix-permissions $HOME
    
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
