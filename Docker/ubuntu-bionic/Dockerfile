FROM ubuntu:18.04

ARG DEBIAN_FRONTEND=noninteractive

ENV container docker

RUN echo 'Acquire::http { Proxy "http://172.17.0.1:3142"; };' > /etc/apt/apt.conf.d/00aptproxy \
	&& apt-get update \
	&& yes | unminimize \
	&& apt-get install -y \
		avahi-daemon \
		cron \
		dbus \
		htop \
		iputils-ping \
		iproute2 \
		less \
		man-db \
		nano \
		openssh-server \
		screen \
		sudo \
		systemd \
		unzip \
		tzdata \
	&& rm -rf /var/lib/apt/lists/*
	
# https://github.com/defn/docker-systemd
	
RUN find /etc/systemd -name '*.timer' | xargs rm -v \
	&& systemctl set-default multi-user.target
	
RUN cp /bin/true /sbin/agetty \
	&& apt-mark hold util-linux 
	
RUN echo exit 0 > /usr/sbin/policy-rc.d

# https://stackoverflow.com/questions/8671308/non-interactive-method-for-dpkg-reconfigure-tzdata	
	
RUN ln -fs /usr/share/zoneinfo/Asia/Jakarta /etc/localtime \
	&& dpkg-reconfigure --frontend noninteractive tzdata
	
RUN mkdir /var/run/sshd
	
RUN adduser --disabled-password --gecos ' ' user \
	&& echo "user:user" | chpasswd \
	&& adduser user sudo

EXPOSE 22

ENTRYPOINT ["/sbin/init", "--log-target=journal"]

CMD []

