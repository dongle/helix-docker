LABEL vendor="Perforce Software"
LABEL maintainer="Paul Allen (pallen@perforce.com)"

FROM ubuntu:bionic

ARG TZ=America/New_York

ENV DEBIAN_FRONTEND=noninteractive \ 
  TZ=$TZ

# Set Timezone
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

# Update Ubuntu and add Perforce Package Source
RUN \
  apt-get update && \
  apt-get install -y apt-utils && \
  apt-get install -y gnupg2 && \
  apt-get install -y wget unzip vim netcat lsb-core net-tools rsyslog && \
  apt-get install -y openjdk-8-jdk && \
  wget -qO - https://package.perforce.com/perforce.pubkey | apt-key add - && \
  echo "deb http://package.perforce.com/apt/ubuntu bionic release" > /etc/apt/sources.list.d/perforce.list && \
  apt-get update
  
RUN \
  apt-get install -y helix-cli && \
  apt-get install -y helix-swarm-triggers

RUN sudo service p4search restart

# ADD http://ftp.perforce.com/perforce/r16.1/bin.java/p4search.tgz /p4search.tgz 

# RUN tar xfz /p4search.tgz

## TODO - add triggers:
#  SEARCH.change    form-commit   change "%//depot/p4search/install/search-queue.sh% -t change   -v %formname%"
#  SEARCH.commit    change-commit //...  "%//depot/p4search/install/search-queue.sh% -t commit   -v %change%"