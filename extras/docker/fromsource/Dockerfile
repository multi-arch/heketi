# set author and base
FROM fedora:28
MAINTAINER Humble Chirammal <hchiramm@redhat.com>

LABEL version="1.0.0"
LABEL description="Kube Development build"

# let's setup all the necessary environment variables
ENV BUILD_HOME=/build
ENV GOPATH=$BUILD_HOME/golang
ENV PATH=$GOPATH/bin:$PATH
ENV HEKETI_BRANCH="master"

COPY ../../.. $GOPATH/src/github.com/heketi/heketi

# install dependencies, build and cleanup
RUN dnf -q -y install glide golang git && \
    dnf -q -y install make && \
    dnf -q -y clean all && \
    cd $GOPATH/src/github.com/heketi/heketi && \
    glide install -v && make && \
    cp heketi /usr/bin/heketi && \
    cp client/cli/go/heketi-cli /usr/bin/heketi-cli && \
    glide cc && \
    cd && rm -rf $BUILD_HOME && \
    dnf -q -y remove git glide golang make && \
    dnf -q -y autoremove && \
    dnf -q -y clean all

# post install config and volume setup
ADD ./heketi.json /etc/heketi/heketi.json
ADD ./topology-sample.json /etc/heketi/topology-sample.json
ADD ./heketi-start.sh /usr/bin/heketi-start.sh
VOLUME /etc/heketi

ADD ./heketi.db /var/lib/heketi/heketi.db
VOLUME /var/lib/heketi

# expose port, set user and set entrypoint with config option
ENTRYPOINT ["/usr/bin/heketi-start.sh"]
EXPOSE 8081
