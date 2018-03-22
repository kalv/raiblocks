FROM ubuntu:16.04

ARG NETWORK=test

ENV BOOST_ROOT=/tmp/boost_install

ADD ci /tmp/ci

RUN apt-get update -qq && apt-get install -yqq \
    build-essential \
    g++ \
    wget && \
    /tmp/ci/bootstrap_boost.sh -m

RUN cd /tmp && \
    wget https://cmake.org/files/v3.11/cmake-3.11.0-rc4.tar.gz && \
    tar xzf cmake-3.11.0-rc4.tar.gz && \
    cd cmake-3.11.0-rc4 && \
    ./configure --prefix=/opt/cmake && \
    make && make install

ADD ./ /tmp/src

RUN mkdir /tmp/build && \
    cd /tmp/build && \
    /opt/cmake/bin/cmake /tmp/src -DBOOST_ROOT=${BOOST_ROOT} -DACTIVE_NETWORK=rai_${NETWORK}_network && \
    make rai_node && \
    cd .. && \
    echo ${NETWORK} > /etc/nano-network

FROM ubuntu:16.04
COPY --from=0 /tmp/build/rai_node /usr/bin
COPY --from=0 /etc/nano-network /etc
COPY docker/node/entry.sh /entry.sh
COPY docker/node/config /usr/share/raiblocks/config
RUN chmod +x /entry.sh
CMD ["/bin/bash",  "/entry.sh"]
