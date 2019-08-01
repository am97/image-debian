## -*- docker-image-name: "scaleway/debian:buster" -*-
ARG DOCKER_ARCH
FROM ${DOCKER_ARCH}/debian:buster
ARG SCW_ARCH


MAINTAINER Scaleway <opensource@scaleway.com> (@scaleway)


# Environment
ENV DEBIAN_FRONTEND=noninteractive SCW_BASE_IMAGE=scaleway/debian:buster


# Add Scaleway PPA
# gnupg needs to be installed before the PPA is added from the overlay/apt
RUN apt-get update && apt-get -y install gnupg
COPY ./overlay/etc/apt/ ./overlay-${SCW_ARCH}*/etc/apt/ /etc/apt/
RUN apt-key adv --keyserver keyserver.ubuntu.com --recv-keys FEC8C91445F9E441


# Adding and calling builder-enter
COPY ./overlay-base/usr/local/sbin/scw-builder-enter /usr/local/sbin/
RUN /bin/sh -e /usr/local/sbin/scw-builder-enter


# Install packages
RUN apt-get -q update && \
    apt-get -y -qq upgrade

RUN apt-get install -y -qq \
	bash-completion \
	bc \
	bootlogd \
	ca-certificates \
  cloud-init \
	cron \
	curl \
	dbus \
	dmidecode \
	dstat \
	ethstatus \
	file \
	fio \
	haveged \
	htop \
	ifupdown \
	ioping \
	iotop \
	iperf \
	iptables \
	iputils-ping \
	isc-dhcp-client \
	kmod \
	less \
	libnss-myhostname \
	linux-image-"$(dpkg --print-architecture | sed 's/armhf/armmp/')" \
	locales \
	locate \
	lsb-release \
	lsof \
	make \
	man-db \
	mg \
	mosh \
	nano \
	net-tools \
	netcat \
	ntp \
	ntpdate \
	python-apt \
	python-yaml \
	rsync \
	rsyslog \
	screen \
	shunit2 \
	socat \
	ssh \
	sudo \
	sysstat \
	systemd-sysv \
	tcpdump \
	tmux \
	uuid-runtime \
	vim \
	wget \
	grub-efi-$(dpkg --print-architecture | sed 's/armhf/arm/') \
	whiptail \
    unattended-upgrades \
    && apt-get clean


# Patch rootfs
RUN rm -f /etc/motd && ln -s /var/run/motd /etc/motd
COPY ./overlay-base/ ./overlay/ ./overlay-${ARCH}*/ /


# Configure locales
RUN locale-gen && dpkg-reconfigure locales


# Configure Systemd
RUN systemctl set-default multi-user
RUN systemctl preset --preset-mode=full $(cat /etc/systemd/system-preset/*scw*.preset | cut -d' ' -f2 | tr '\n' ' ')


# Remove dbus machine-id
RUN rm /var/lib/dbus/machine-id


# Clean rootfs from image-builder
RUN /usr/local/sbin/scw-builder-leave
