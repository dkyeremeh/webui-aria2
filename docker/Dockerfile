FROM debian:latest

# less priviledge user, the id should map the user the downloaded files belongs to
RUN groupadd -r dummy && useradd -r -g dummy dummy -u 1000

# webui + aria2
RUN apt-get update \
  && apt-get install -y aria2 busybox curl \
  && rm -rf /var/lib/apt/lists/*

ADD ./docs /webui-aria2

# gosu install latest
RUN ARCH=$(dpkg --print-architecture) \
  && GOSUDO_REPO=https://github.com/tianon/gosu \
  && LATEST=$(curl -sL $GOSUDO_REPO/releases/latest | grep -Eo "[0-9]\.[0-9]+" | head -n1) \
  && curl -Ls "$GOSUDO_REPO/releases/download/$LATEST/gosu-$ARCH" > /usr/local/bin/gosu \
  && chmod +x ./usr/local/bin/gosu

# goreman supervisor install latest
RUN ARCH=$(dpkg --print-architecture) \
  && GOREMAN_REPO=https://github.com/mattn/goreman \
  && LATEST=$(curl -sL  $GOREMAN_REPO/releases/latest | grep -Eo "v[0-9]+.[0-9]+.[0-9]+" | head -n1) \
  && curl -Ls $GOREMAN_REPO"/releases/download/"$LATEST"/goreman_"$LATEST"_linux_$ARCH.tar.gz" > goreman.tar.gz \
  && tar xvf goreman.tar.gz && mv /goreman*/goreman /usr/local/bin/goreman && rm -R goreman*

# goreman setup
RUN echo "web: gosu dummy /bin/busybox httpd -f -p 8080 -h /webui-aria2\nbackend: gosu dummy /usr/bin/aria2c --enable-rpc --rpc-listen-all --dir=/data" > Procfile

# aria2 downloads directory
VOLUME /data

# aria2 RPC port, map as-is or reconfigure webui
EXPOSE 6800/tcp

# webui static content web server, map wherever is convenient
EXPOSE 8080/tcp

CMD ["start"]
ENTRYPOINT ["/usr/local/bin/goreman"]
