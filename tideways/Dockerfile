FROM debian:stretch

RUN apt-get update && \
    apt-get install -yq wget gnupg && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

RUN echo 'deb http://s3-eu-west-1.amazonaws.com/tideways/packages debian main' >> /etc/apt/sources.list.d/tideways.list && \
    wget -qO - https://s3-eu-west-1.amazonaws.com/tideways/packages/EEB5E8F4.gpg | apt-key add - && \
    apt-get update && \
    DEBIAN_FRONTEND=noninteractive apt-get install -yq tideways-daemon && \
    apt-get autoremove --assume-yes && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

ADD run.sh /run.sh

CMD ["/run.sh"]