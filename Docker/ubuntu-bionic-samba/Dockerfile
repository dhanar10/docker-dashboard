FROM ubuntu:18.04

ARG DEBIAN_FRONTEND=noninteractive

ENV container docker

RUN echo 'Acquire::http { Proxy "http://172.17.0.1:3142"; };' > /etc/apt/apt.conf.d/00aptproxy \
	&& apt-get update && apt-get install -y \
		avahi-daemon \
		cron \
		dbus \
		htop \
		iputils-ping \
		iproute2 \
		less \
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

# https://stackoverflow.com/questions/8671308/non-interactive-method-for-dpkg-reconfigure-tzdata	
	
RUN ln -fs /usr/share/zoneinfo/Asia/Jakarta /etc/localtime \
	&& dpkg-reconfigure --frontend noninteractive tzdata
	
RUN mkdir /var/run/sshd
	
RUN adduser --disabled-password --gecos ' ' user \
	&& echo "user:user" | chpasswd \
	&& adduser user sudo
	
RUN apt-get update && apt-get install -y \
		samba \
		samba-common \
		python-dnspython \
	&& rm -rf /var/lib/apt/lists/*
RUN mkdir -p /storage \
	&& chmod -R 755 /storage \
	&& chown nobody:nogroup /storage
COPY smb.conf /etc/samba/smb.conf

EXPOSE 22
EXPOSE 137
EXPOSE 138
EXPOSE 139
EXPOSE 445

ENTRYPOINT ["/sbin/init", "--log-target=journal"]

CMD []

