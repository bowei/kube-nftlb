# Download the latest Debian image
FROM debian:stable

# Default shell when executing RUN
SHELL ["/bin/bash", "-c"]


RUN cat /etc/apt/sources.list

# Put Debian in not interactive mode
RUN echo 'debconf debconf/frontend select Noninteractive' | debconf-set-selections

RUN apt-get update
RUN apt-get install -y gnupg ca-certificates wget procps


# Install nftlb and dependencies
RUN wget -O - http://repo.zevenet.com/zevenet.com.gpg.key | apt-key add -
RUN echo "deb [arch=amd64] http://repo.zevenet.com/ce/v5 buster main" | tee -a /etc/apt/sources.list

RUN apt-get update
RUN apt-get install -y nftlb


# Replacing nftlb with a devel version
COPY ./nftlb.deb /nftlb.deb
RUN if [ -s "nftlb.deb" ] ; then dpkg -i ./nftlb.deb ; fi


# load container parameters
COPY ./container_params.conf /container_params.conf


# After compiling the Golang client, copy the binary to / and make it the entrypoint
COPY ./app /goclient

COPY ./start.sh /start.sh
RUN source /container_params.conf && \
	sed -Ei "s/#KEY#/$nftlbKey/g" /start.sh && \
	sed -Ei "s/#DAEMONCHECKTIMEOUT#/$daemonCheckTimeout/g" /start.sh

CMD [ "/bin/bash", "-c", "/start.sh" ]
