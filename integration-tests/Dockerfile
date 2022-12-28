FROM tensorflow/tensorflow:2.5.1-gpu

RUN apt-get update --yes

RUN apt-get install --yes systemd curl git sudo wget nano htop zip

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
# ENV TLJH_BOOTSTRAP_DEV=yes
# ENV TLJH_BOOTSTRAP_PIP_SPEC=/srv/src
# ENV PATH=/opt/tljh/hub/bin:${PATH}

#  --mount type=bind,source=$(pwd),target=/srv/src
COPY / /srv/src

CMD ["/bin/bash", "-c", "exec /sbin/init --log-target=journal 3>&1"]
