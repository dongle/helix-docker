FROM ubuntu:bionic

LABEL vendor="Perforce Software"
LABEL maintainer="Paul Allen (pallen@perforce.com)"


# --------------------------------------------------------------------------------
# Docker ENVIRONMENT
# --------------------------------------------------------------------------------

# Default Environment
ARG P4PORT=p4d.helix:1666
ARG P4NAME=build
ARG P4TCP=1666
ARG P4USER=super
ARG P4PASSWD=Passw0rd
ARG P4CASE=-C1
ARG P4CHARSET=utf8
ARG SERVICEUSER=service
ARG SERVICEGROUP=unlimited
ARG TZ=America/New_York

# Dynamic Environment
ENV P4PORT=$P4PORT \
  P4NAME=$P4NAME \
  P4TCP=$P4TCP \
  P4USER=$P4USER \
  P4PASSWD=$P4PASSWD \
  P4CASE=$P4CASE \
  P4CHARSET=$P4CHARSET \
  JNL_PREFIX=$P4NAME \
  SERVICEUSER=$SERVICEUSER \
  SERVICEGROUP=$SERVICEGROUP \
  DEBIAN_FRONTEND=noninteractive \
  TZ=$TZ

# Base Environment
ENV P4HOME=/p4
ENV P4LOGDIR=$P4HOME/log

# Derived Environment
ENV P4ROOT=$P4HOME/root \
  P4DEPOTS=$P4HOME/depots \
  P4CKP=$P4HOME/checkpoints \
  P4JOURNAL=$P4LOGDIR/journal \
  P4LOG=$P4LOGDIR/p4d.log



# --------------------------------------------------------------------------------
# Docker configuration for Build Replica
# --------------------------------------------------------------------------------

# Set Timezone
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

# Update Ubuntu and add Perforce Package Source
RUN \
  apt-get update && \
  apt-get install -y apt-utils && \
  apt-get install -y gnupg2 && \
  apt-get install -y wget unzip vim iputils-ping && \
  wget -qO - https://package.perforce.com/perforce.pubkey | apt-key add - && \
  echo "deb http://package.perforce.com/apt/ubuntu bionic release" > /etc/apt/sources.list.d/perforce.list && \
  apt-get update



# --------------------------------------------------------------------------------
# Docker BUILD
# --------------------------------------------------------------------------------

# Install Perforce Server
RUN apt-get install -y helix-p4d

# Add external files
COPY files/clean.sh /usr/local/bin/clean.sh
COPY files/restore.sh /usr/local/bin/restore.sh
COPY files/replica_setup.sh /usr/local/bin/replica_setup.sh
COPY files/latest_checkpoint.sh /usr/local/bin/latest_checkpoint.sh

RUN \
  chmod +x /usr/local/bin/clean.sh && \
  chmod +x /usr/local/bin/restore.sh && \
  chmod +x /usr/local/bin/replica_setup.sh && \
  chmod +x /usr/local/bin/latest_checkpoint.sh && \
  mkdir -p $P4ROOT && \
  mkdir -p $P4DEPOTS && \
  mkdir -p $P4CKP && \
  mkdir -p $P4LOGDIR

# Expose Perforce; TCP port and volumes
EXPOSE $P4TCP
VOLUME $P4HOME

# --------------------------------------------------------------------------------
# Docker RUN
# --------------------------------------------------------------------------------

ENTRYPOINT \
  replica_setup.sh && \
  bash

HEALTHCHECK \
  --interval=2m \
  --timeout=10s \
  CMD p4 -p $P4TCP info -s > /dev/null || exit 1
