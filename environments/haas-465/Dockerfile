FROM ubuntu:20.04
ENV DEBIAN_FRONTEND=nonintercative

RUN apt-get update && apt-get install -y autoconf automake curl cmake git libtool make && apt-get -y upgrade

RUN git clone --depth=1 https://github.com/tsl0922/ttyd.git /ttyd

RUN cd /ttyd && env BUILD_TARGET=x86_64 WITH_SSL=true ZLIB_VERSION=1.2.12 ./scripts/cross-build.sh

FROM ubuntu:20.04
COPY --from=0 /ttyd/build/ttyd /usr/bin/ttyd

RUN apt-get update \
    && apt-get install -y vim net-tools dnsutils curl wget apt-transport-https gnupg2 traceroute open-vm-tools tcpdump jq iproute2 openssh-client bash-completion && apt-get -y upgrade\
    && curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | apt-key add - \
    && echo "deb https://apt.kubernetes.io/ kubernetes-xenial main" | tee -a /etc/apt/sources.list.d/kubernetes.list \
    && apt-get update \
    && apt-get install -y kubectl

#RUN mkdir -p /root/.bitfusion
#RUN mkdir -p /workspace/bitfusion
#WORKDIR /workspace/bitfusion
#COPY ./bitfusion-client-ubuntu2004_4.5.0-4_amd64.deb .
#RUN apt-get update \
#    && apt-get install -y ./bitfusion-client-ubuntu2004_4.5.0-4_amd64.deb


ADD https://github.com/krallin/tini/releases/download/v0.19.0/tini /sbin/tini
RUN chmod +x /sbin/tini

RUN echo ". /usr/share/bash-completion/bash_completion" >> /etc/bash.bashrc
RUN echo "source <(kubectl completion bash)" >> /etc/bash.bashrc
RUN sed -i 's/#force_color_prompt=yes/force_color_prompt=yes/' /root/.bashrc

EXPOSE 7681
ENTRYPOINT ["/sbin/tini", "--"]
CMD ["ttyd", "bash"]