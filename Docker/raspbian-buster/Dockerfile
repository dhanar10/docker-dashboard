FROM scratch

ARG DEBIAN_FRONTEND=noninteractive

ENV container docker

# http://downloads.raspberrypi.org/raspbian_lite/archive/2020-02-14-13:49/boot.tar.xz
# http://downloads.raspberrypi.org/raspbian_lite/archive/2020-02-14-13:49/root.tar.xz
	
ADD root.tar.xz /
ADD boot.tar.xz /boot/

ADD qemu-arm-static.tar.xz /usr/bin/
RUN sed -i 's/^/#/' /etc/ld.so.preload

RUN echo 'Acquire::http { Proxy "http://172.17.0.1:3142"; };' > /etc/apt/apt.conf.d/00aptproxy

# https://github.com/defn/docker-systemd
	
RUN find /etc/systemd -name '*.timer' | xargs rm -v \
	&& systemctl set-default multi-user.target

# https://stackoverflow.com/questions/8671308/non-interactive-method-for-dpkg-reconfigure-tzdata	
	
RUN ln -fs /usr/share/zoneinfo/Asia/Jakarta /etc/localtime \
	&& dpkg-reconfigure --frontend noninteractive tzdata
	
RUN dpkg-reconfigure openssh-server \
	&& systemctl enable ssh \
	&& mkdir /var/run/sshd
	
RUN systemctl disable avahi-daemon.socket \
	&& systemctl disable avahi-daemon.service
	
RUN apt-get update \
	&& apt-get install -y \
		python3-pip \
	&& rm -rf /var/lib/apt/lists/*

ADD mdns-proxy.py /usr/local/bin/
ADD slimDNS.py /usr/local/lib/python3.5/dist-packages/
ADD mdns-proxy.service mdns-proxy-resolv.service /etc/systemd/system/
RUN pip3 install dnslib \
	&& chmod +x /usr/local/bin/mdns-proxy.py \
	&& systemctl enable mdns-proxy \
	&& systemctl enable mdns-proxy-resolv

EXPOSE 22

ENTRYPOINT ["/sbin/init", "--log-target=journal"]

CMD []
