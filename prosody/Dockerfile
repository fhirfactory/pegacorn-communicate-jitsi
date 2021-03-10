FROM fhirfactory/pegacorn-base-docker-jitsi:1.0.0 as builder

RUN \
    apt-dpkg-wrap apt-get update \
    && apt-dpkg-wrap apt-get install -y \
      lua5.2 \
      liblua5.2-dev \
      libsasl2-dev \
      libssl-dev \
      luarocks \
      git \
      gcc \
    && luarocks install cyrussasl 1.1.0-1 \
    && luarocks install net-url 0.9-1 \
    && luarocks install luajwtjitsi 2.0-0

FROM fhirfactory/pegacorn-base-docker-jitsi:1.0.0

ENV XMPP_CROSS_DOMAIN="false"

RUN \
    wget -q https://prosody.im/files/prosody-debian-packages.key -O - | gpg --enarmor > /etc/apt/trusted.gpg.d/prosody.asc \
    && echo "deb http://packages.prosody.im/debian buster main" > /etc/apt/sources.list.d/prosody.list \
    && apt-dpkg-wrap apt-get update \
    && apt-dpkg-wrap apt-get install -y \
      prosody \
      libssl1.1 \
      sasl2-bin \
      libsasl2-modules-ldap \
      lua-basexx \
      lua-ldap \
      lua-sec \
      patch \
    && apt-cleanup \
    && rm -rf /etc/prosody

RUN \
    apt-dpkg-wrap apt-get update \
    && apt-dpkg-wrap apt-get -d install -y jitsi-meet-prosody \
    && dpkg -x /var/cache/apt/archives/jitsi-meet-prosody*.deb /tmp/pkg \
    && mv /tmp/pkg/usr/share/jitsi-meet/prosody-plugins /prosody-plugins \
    && apt-cleanup \
    && rm -rf /tmp/pkg /var/cache/apt

RUN patch -d /usr/lib/prosody/modules/muc -p0 < /prosody-plugins/muc_owner_allow_kick.patch

COPY rootfs/ /

COPY --from=builder /usr/local/lib/lua /usr/local/lib/lua
COPY --from=builder /usr/local/share/lua /usr/local/share/lua

# Date-time build argument
ARG IMAGE_BUILD_TIMESTAMP
ENV IMAGE_BUILD_TIMESTAMP=${IMAGE_BUILD_TIMESTAMP}
RUN echo IMAGE_BUILD_TIMESTAMP=${IMAGE_BUILD_TIMESTAMP}

EXPOSE 5222 5347 5280

VOLUME ["/config", "/prosody-plugins-custom"]
