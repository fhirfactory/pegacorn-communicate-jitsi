FROM fhirfactory/pegacorn-base-docker-jitsi:1.0.0

ADD https://raw.githubusercontent.com/acmesh-official/acme.sh/2.8.8/acme.sh /opt
COPY rootfs/ /

RUN \
	apt-dpkg-wrap apt-get update && \
	apt-dpkg-wrap apt-get install -y cron nginx-extras jitsi-meet-web socat nano && \
	apt-dpkg-wrap apt-get -d install -y jitsi-meet-web-config && \
    dpkg -x /var/cache/apt/archives/jitsi-meet-web-config*.deb /tmp/pkg && \
    mv /tmp/pkg/usr/share/jitsi-meet-web-config/config.js /defaults && \
	mv /usr/share/jitsi-meet/interface_config.js /defaults && \
	rm -f /etc/nginx/conf.d/default.conf && \
	apt-cleanup && \
	rm -rf /tmp/pkg /var/cache/apt

COPY pegacorn-communicate-jitsi.site-a.key /config/keys
COPY pegacorn-communicate-jitsi.site-a.crt /config/keys

# Date-time build argument
ARG IMAGE_BUILD_TIMESTAMP
ENV IMAGE_BUILD_TIMESTAMP=${IMAGE_BUILD_TIMESTAMP}
RUN echo IMAGE_BUILD_TIMESTAMP=${IMAGE_BUILD_TIMESTAMP}

EXPOSE 80 443

VOLUME ["/config", "/usr/share/jitsi-meet/transcripts"]
