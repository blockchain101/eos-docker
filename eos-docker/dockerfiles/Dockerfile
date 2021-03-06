FROM blockchain101/eos-baseimage:dawn3.0 as baseimage
ARG branch=master
RUN git clone -b $branch --depth 1 https://github.com/EOSIO/eos.git --recursive 
RUN cd eos && echo "$branch:$(git rev-parse HEAD)" > /etc/eosio-version \
    && cmake -H. -B"/tmp/build" -GNinja -DCMAKE_BUILD_TYPE=Release -DWASM_ROOT=/opt/wasm -DCMAKE_CXX_COMPILER=clang++ \
       -DCMAKE_C_COMPILER=clang -DCMAKE_INSTALL_PREFIX=/tmp/build  -DSecp256k1_ROOT_DIR=/usr/local -DBUILD_MONGO_DB_PLUGIN=true \
    && cmake --build /tmp/build --target install


FROM ubuntu:16.04
LABEL author="Peng Zhuang <rodger0514@163.com>" \
  maintainer="Peng Zhuang <rodger0514@163.com>" \
  version="0.3.0" \
  description="A blockchain101/eos image"

RUN apt-get update && DEBIAN_FRONTEND=noninteractive apt-get -y install openssl && rm -rf /var/lib/apt/lists/*
COPY --from=baseimage /usr/local/lib/* /usr/local/lib/
COPY --from=baseimage /tmp/build/bin /opt/eosio/bin
COPY --from=baseimage /tmp/build/contracts /contracts
COPY --from=baseimage /eos/Docker/config.ini /
COPY --from=baseimage /etc/eosio-version /etc
COPY nodeosd.sh /opt/eosio/bin/nodeosd.sh
ENV EOSIO_ROOT=/opt/eosio
RUN chmod +x /opt/eosio/bin/nodeosd.sh
ENV LD_LIBRARY_PATH /usr/local/lib
VOLUME /opt/eosio/bin/data-dir
ENV PATH /opt/eosio/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
