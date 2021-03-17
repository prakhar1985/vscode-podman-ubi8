FROM registry.access.redhat.com/ubi8/ubi-init:latest AS haproxy

RUN yum install -y haproxy



RUN systemctl enable haproxy
EXPOSE 8443
CMD [ "/sbin/init" ]
