# We want to do things using Debian Buster's own Python
FROM debian:buster

# make Apt non-interactive
RUN echo 'APT::Get::Assume-Yes "true";' > /etc/apt/apt.conf.d/90circleci \
  && echo 'DPkg::Options "--force-confnew";' >> /etc/apt/apt.conf.d/90circleci

ENV DEBIAN_FRONTEND=noninteractive
# Make sure PATH includes ~/.local/bin
# https://bugs.debian.org/cgi-bin/bugreport.cgi?bug=839155
# This only works for root. The circleci user is done near the end of this Dockerfile
RUN echo 'PATH="$HOME/.local/bin:$PATH"' >> /etc/profile.d/user-local-path.sh

# man directory is missing in some base images
# https://bugs.debian.org/cgi-bin/bugreport.cgi?bug=863199
RUN apt-get update \
  && mkdir -p /usr/share/man/man1 \
  && apt-get -y install \
    git mercurial xvfb apt \
    locales sudo openssh-client ca-certificates tar gzip parallel \
    net-tools netcat unzip zip bzip2 gnupg curl wget make python3 python3-venv python3-pip

# apt (2.3.12) unstable; urgency=medium
# The solver will no longer try to remove Essential or Protected packages,
# any dependency problem that would need such a solution will have to be
# resolved manually.

RUN apt-get clean

# Set timezone to UTC by default
RUN ln -sf /usr/share/zoneinfo/Etc/UTC /etc/localtime

# Use unicode
RUN locale-gen C.UTF-8 || true
ENV LANG=C.UTF-8

RUN groupadd --gid 3434 ci \
  && useradd --uid 3434 --gid ci --shell /bin/bash --create-home ci \
  && echo 'ci ALL=NOPASSWD: ALL' >> /etc/sudoers.d/50-ci \
  && echo 'Defaults    env_keep += "DEBIAN_FRONTEND"' >> /etc/sudoers.d/env_keep



USER ci
ENV PATH /home/ci/.local/bin:/home/ci/bin:${PATH}

CMD ["/bin/sh"]
