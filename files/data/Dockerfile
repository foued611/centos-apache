FROM feduxorg/centos
MAINTAINER dev@fedux.org

RUN mkdir -p /var/log/apache \
    && chown -R apache:apache /var/log/apache

USER apache

VOLUME ["/var/log/apache"]
