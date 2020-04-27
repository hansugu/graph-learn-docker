FROM ubuntu:18.04

RUN apt-get update && apt-get install -y --no-install-recommends \
    ant \
    autoconf \
    build-essential \
    cmake \
    default-jre \
    golang-go \
    python \
    python-pip \
    python-setuptools \
    wget \
    vim \
    tmux \
    zookeeper \
    htop \
    dos2unix \
    sudo \
    bzip2 \
    openssh-server \
    && \
  apt-get clean && \
  rm -rf /var/lib/apt/lists/*

RUN wget --quiet https://repo.anaconda.com/archive/Anaconda2-2019.10-Linux-x86_64.sh -O ~/anaconda.sh && \
    /bin/bash ~/anaconda.sh -b -p /opt/conda && \
    rm ~/anaconda.sh && \
    ln -s /opt/conda/etc/profile.d/conda.sh /etc/profile.d/conda.sh && \
    echo ". /opt/conda/etc/profile.d/conda.sh" >> ~/.bashrc && \
    echo "conda activate base" >> ~/.bashrc

RUN /opt/conda/bin/conda install -y tensorflow=1.12.0

RUN apt update && apt install -y git libz-dev

#openssl required by grpc
RUN cd /root \
&& wget https://www.openssl.org/source/old/1.0.2/openssl-1.0.2k.tar.gz \
&& tar -xzvf openssl-1.0.2k.tar.gz \
&& cd openssl-1.0.2k \
&& ./config shared zlib-dynamic \
&& make -j 32 \
&& mkdir -p /usr/lib64 \
&& cp libcrypto.so.1.0.0 /usr/lib64/ \
&& cp libssl.so.1.0.0 /usr/lib64/ \
&& cd /usr/lib64 \
&&  ln -s libssl.so.1.0.0 libssl.so.10 \
&&  ln -s libcrypto.so.1.0.0 libcrypto.so.10
ENV LD_LIBRARY_PATH=/usr/lib64:$LD_LIBRARY_PATH

#install graph-learn
RUN mkdir -p /workspace
RUN cd /workspace && wget https://github.com/hansugu/graph-learn-docker/releases/download/binaries/graphlearn-0.11-cp27-cp27mu-linux_x86_64.whl
#ADD graphlearn-0.1-cp27-cp27mu-linux_x86_64.whl /workspace
#ADD requirements.txt /workspace
RUN cd /workspace && wget https://github.com/hansugu/graph-learn-docker/releases/download/binaries/requirements.txt
RUN /opt/conda/bin/pip install -I /workspace/graphlearn-0.11-cp27-cp27mu-linux_x86_64.whl
RUN /opt/conda/bin/pip install -r /workspace/requirements.txt

# Enable ssh
RUN mkdir /var/run/sshd
RUN echo 'root:admin' | chpasswd
RUN sed -i 's/PermitRootLogin prohibit-password//' /etc/ssh/sshd_config && echo "PermitRootLogin yes" >> /etc/ssh/sshd_config

# SSH login fix. Otherwise user is kicked off after login
RUN sed 's@session\s*required\s*pam_loginuid.so@session optional pam_loginuid.so@g' -i /etc/pam.d/sshd

EXPOSE 22
CMD ["/usr/sbin/sshd", "-D"]

# download code
#RUN cd /workspace && git clone https://github.com/alibaba/graph-learn.git && cd graph-learn/examples && /opt/conda/bin/pip install -r requirements.txt
